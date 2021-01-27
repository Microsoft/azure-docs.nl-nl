---
title: Problemen met Azure Automation Hybrid Runbook Worker oplossen
description: In dit artikel leest u hoe u problemen kunt oplossen en oplossen die zich voordoen bij Azure Automation Hybrid Runbook Workers.
services: automation
ms.subservice: ''
ms.date: 11/25/2019
ms.topic: troubleshooting
ms.openlocfilehash: 214501c447632232dc00b61643ea21083bd0e4ac
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/27/2021
ms.locfileid: "98896491"
---
# <a name="troubleshoot-hybrid-runbook-worker-issues"></a>Problemen met Hybrid Runbook Worker oplossen

In dit artikel vindt u informatie over het oplossen van problemen met Azure Automation Hybrid Runbook Workers. Zie [overzicht van Hybrid Runbook worker](../automation-hybrid-runbook-worker.md)voor algemene informatie.

## <a name="general"></a>Algemeen

De Hybrid Runbook Worker is afhankelijk van een agent om te communiceren met uw Azure Automation-account om de werk nemer te registreren, Runbook-taken te ontvangen en de status van het rapport. Voor Windows is deze agent de Log Analytics-agent voor Windows. Voor Linux is het de Log Analytics-agent voor Linux.

### <a name="scenario-runbook-execution-fails"></a><a name="runbook-execution-fails"></a>Scenario: het uitvoeren van een Runbook mislukt

#### <a name="issue"></a>Probleem

Het Runbook kan niet worden uitgevoerd en het volgende fout bericht wordt weer gegeven:

```error
"The job action 'Activate' cannot be run, because the process stopped unexpectedly. The job action was attempted three times."
```

Het runbook is onderbroken kort nadat het drie keer is geprobeerd uit te voeren. Er zijn voor waarden die het runbook kunnen onderbreken van volt ooien. Het gerelateerde fout bericht bevat mogelijk geen aanvullende informatie.

#### <a name="cause"></a>Oorzaak

Hier volgen enkele mogelijke oorzaken:

* De runbooks kunnen niet worden geverifieerd met lokale resources.
* De Hybrid worker bevindt zich achter een proxy of firewall.
* De computer die is geconfigureerd voor het uitvoeren van de Hybrid Runbook Worker voldoet niet aan de minimale hardwarevereisten.

#### <a name="resolution"></a>Oplossing

Controleer of de computer uitgaande toegang heeft tot **\* . Azure-Automation.net** op poort 443.

Computers die de Hybrid Runbook Worker uitvoeren, moeten voldoen aan de minimale hardwarevereisten voordat de werk nemer is geconfigureerd voor het hosten van deze functie. Runbooks en het achtergrond proces dat door ze worden gebruikt, kunnen ertoe leiden dat het systeem wordt overtreden en er vertragingen of time-outs optreden in een runbook-taak.

Controleer of de computer waarop de Hybrid Runbook Worker-functie moet worden uitgevoerd, voldoet aan de minimale hardwarevereisten. Als dit het geval is, controleert u het CPU-en geheugen gebruik om de correlatie tussen de prestaties van Hybrid Runbook Worker processen en Windows te bepalen. Elk geheugen of CPU-druk kan erop wijzen dat er resources moeten worden bijgewerkt. U kunt ook een andere compute-resource selecteren die ondersteuning biedt voor de minimale vereisten en schalen wanneer de werk belasting vereist dat een toename nood zakelijk is.

Raadpleeg het **micro soft-SMA-** gebeurtenis logboek voor een bijbehorende gebeurtenis met de beschrijving `Win32 Process Exited with code [4294967295]` . De oorzaak van deze fout is dat u geen authenticatie hebt geconfigureerd in uw runbooks of dat u de run as-referenties voor de groep Hybrid Runbook Worker hebt opgegeven. Controleer de runbook-machtigingen voor het [uitvoeren van runbooks op een Hybrid Runbook worker](../automation-hrw-run-runbooks.md) om te bevestigen dat u de verificatie voor uw runbooks juist hebt geconfigureerd.

### <a name="scenario-event-15011-in-the-hybrid-runbook-worker"></a><a name="cannot-connect-signalr"></a>Scenario: gebeurtenis 15011 in de Hybrid Runbook Worker

#### <a name="issue"></a>Probleem

De Hybrid Runbook Worker ontvangt gebeurtenis 15011 om aan te geven dat een query resultaat ongeldig is. De volgende fout wordt weer gegeven wanneer de werk nemer een verbinding met de [signaal server](/aspnet/core/signalr/introduction)probeert te openen.

```error
[AccountId={c7d22bd3-47b2-4144-bf88-97940102f6ca}]
[Uri=https://cc-jobruntimedata-prod-su1.azure-automation.net/notifications/hub][Exception=System.TimeoutException: Transport timed out trying to connect
   at System.Runtime.ExceptionServices.ExceptionDispatchInfo.Throw()
   at System.Runtime.CompilerServices.TaskAwaiter.HandleNonSuccessAndDebuggerNotification(Task task)
   at JobRuntimeData.NotificationsClient.JobRuntimeDataServiceSignalRClient.<Start>d__45.MoveNext()
```

#### <a name="cause"></a>Oorzaak

De Hybrid Runbook Worker is niet juist geconfigureerd voor de functie voor automatische implementatie, bijvoorbeeld voor Updatebeheer. De implementatie bevat een deel waarmee de virtuele machine wordt verbonden met de Log Analytics-werk ruimte. Het Power shell-script zoekt naar de werk ruimte in het abonnement met de opgegeven naam. In dit geval bevindt de Log Analytics-werk ruimte zich in een ander abonnement. Het script kan de werk ruimte niet vinden en probeert er een te maken, maar de naam wordt al gebruikt. Als gevolg hiervan mislukt de implementatie.

#### <a name="resolution"></a>Oplossing

U kunt dit probleem op twee manieren oplossen:

* Wijzig het Power shell-script om te zoeken naar de Log Analytics-werk ruimte in een ander abonnement. Dit is een goede oplossing voor gebruik als u in de toekomst veel Hybrid Runbook Worker machines wilt implementeren.

* Configureer de werk computer voor het uitvoeren van een Orchestrator-sandbox hand matig. Voer vervolgens een runbook uit dat is gemaakt in het Azure Automation-account op de werk nemer om de functionaliteit te testen.

### <a name="scenario-windows-azure-vms-automatically-dropped-from-a-hybrid-worker-group"></a><a name="vm-automatically-dropped"></a>Scenario: Windows Azure-Vm's worden automatisch verwijderd uit een Hybrid worker-groep

#### <a name="issue"></a>Probleem

U kunt de Hybrid Runbook Worker of Vm's niet zien wanneer de werk computer gedurende een lange periode is uitgeschakeld.

#### <a name="cause"></a>Oorzaak

De Hybrid Runbook Worker computer is al meer dan 30 dagen niet gepingd Azure Automation. Als gevolg hiervan heeft de Hybrid Runbook Worker groep of de systeem werk groep leeg gemaakt. 

#### <a name="resolution"></a>Oplossing

Start de werk computer en rereregister deze met Azure Automation. Zie [Deploying a Windows Hybrid Runbook worker](../automation-windows-hrw-install.md)(Engelstalig) voor instructies over het installeren van de runbook-omgeving en het maken van verbinding met Azure Automation.

### <a name="scenario-no-certificate-was-found-in-the-certificate-store-on-the-hybrid-runbook-worker"></a><a name="no-cert-found"></a>Scenario: er is geen certificaat gevonden in het certificaat archief op de Hybrid Runbook Worker

#### <a name="issue"></a>Probleem

Een runbook dat wordt uitgevoerd op een Hybrid Runbook Worker mislukt met het volgende fout bericht:

```error
Connect-AzAccount : No certificate was found in the certificate store with thumbprint 0000000000000000000000000000000000000000
At line:3 char:1
+ Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID -Appl ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : CloseError: (:) [Connect-AzAccount], ArgumentException
    + FullyQualifiedErrorId : Microsoft.Azure.Commands.Profile.ConnectAzAccountCommand
```
#### <a name="cause"></a>Oorzaak

Deze fout treedt op wanneer u een [uitvoeren als-account](../manage-runas-account.md) probeert te gebruiken in een runbook dat wordt uitgevoerd op een Hybrid Runbook worker waarbij het certificaat van het uitvoeren als-account niet aanwezig is. Hybrid Runbook Workers hebben standaard het certificaat Asset niet lokaal. Voor het uitvoeren als-account moet deze asset goed functioneren.

#### <a name="resolution"></a>Oplossing

Als uw Hybrid Runbook Worker een Azure-VM is, kunt u in plaats daarvan [Runbook-verificatie met beheerde identiteiten](../automation-hrw-run-runbooks.md#runbook-auth-managed-identities) gebruiken. Dit scenario vereenvoudigt verificatie door u in plaats van het run as-account te verifiëren bij Azure-resources met behulp van de beheerde identiteit van de virtuele Azure-machine. Wanneer de Hybrid Runbook Worker een on-premises machine is, moet u het certificaat van het uitvoeren als-account op de computer installeren. Zie de stappen voor het uitvoeren van de Power shell **-RunAsCertificateToHybridWorker** in [runbooks uitvoeren op een Hybrid Runbook worker](../automation-hrw-run-runbooks.md)voor meer informatie over het installeren van het certificaat.

### <a name="scenario-error-403-during-registration-of-a-hybrid-runbook-worker"></a><a name="error-403-on-registration"></a>Scenario: fout 403 tijdens de registratie van een Hybrid Runbook Worker

#### <a name="issue"></a>Probleem

De eerste registratie fase van de werk nemer mislukt en u ontvangt de volgende fout (403):

```error
"Forbidden: You don't have permission to access / on this server."
```

#### <a name="cause"></a>Oorzaak

De volgende problemen zijn mogelijke oorzaken:

* Er is een niet-getypte werk ruimte-ID of werkruimte sleutel (primair) aanwezig in de instellingen van de agent. 
* De Hybrid Runbook Worker kan de configuratie niet downloaden, waardoor een fout in de account koppeling wordt veroorzaakt. Wanneer Azure functies op computers inschakelt, worden alleen bepaalde regio's ondersteund voor het koppelen van een Log Analytics-werk ruimte en een Automation-account. Het is ook mogelijk dat er een onjuiste datum of tijd op de computer is ingesteld. Als de tijd +/-15 minuten vanaf de huidige tijd is, mislukt de implementatie van de functie.

#### <a name="resolution"></a>Oplossing

##### <a name="mistyped-workspace-id-or-key"></a>Niet-getypte werk ruimte-ID of-sleutel
Als u wilt controleren of de werk ruimte-ID of de werk ruimte sleutel van de agent is getypt, raadpleegt u [een werk ruimte toevoegen of verwijderen: Windows agent](../../azure-monitor/platform/agent-manage.md#windows-agent) voor de Windows-agent of [een werk ruimte toevoegen of verwijderen. Dit is een Linux-agent](../../azure-monitor/platform/agent-manage.md#linux-agent) voor de Linux-agent. Zorg ervoor dat u de volledige teken reeks selecteert in het Azure Portal en kopieer en plak deze zorgvuldig.

##### <a name="configuration-not-downloaded"></a>De configuratie is niet gedownload

Uw Log Analytics-werk ruimte en het Automation-account moeten zich in een gekoppelde regio bevinden. Zie [Azure Automation en log Analytics werkruimte toewijzingen](../how-to/region-mappings.md)voor een lijst met ondersteunde regio's.

Mogelijk moet u ook de datum of tijd zone van uw computer bijwerken. Als u een aangepast tijds bereik selecteert, moet u ervoor zorgen dat het bereik zich in UTC bevindt. Dit kan afwijken van uw lokale tijd zone.

## <a name="linux"></a>Linux

De Linux-Hybrid Runbook Worker is afhankelijk van de [log Analytics-agent voor Linux](../../azure-monitor/platform/log-analytics-agent.md) om te communiceren met uw Automation-account om de werk nemer te registreren, Runbook-taken te ontvangen en de status van het rapport. Als de registratie van de werk nemer mislukt, zijn hier enkele mogelijke oorzaken voor de fout.

### <a name="scenario-linux-hybrid-runbook-worker-receives-prompt-for-a-password-when-signing-a-runbook"></a><a name="prompt-for-password"></a>Scenario: Linux Hybrid Runbook Worker wordt om een wacht woord gevraagd bij het ondertekenen van een Runbook

#### <a name="issue"></a>Probleem

Als u de `sudo` opdracht uitvoert voor een Linux-Hybrid Runbook worker wordt een onverwachte prompt voor een wacht woord opgehaald.

#### <a name="cause"></a>Oorzaak

Het **nxautomationuser** -account voor de log Analytics-agent voor Linux is niet juist geconfigureerd in het **sudo** -bestand. De Hybrid Runbook Worker heeft de juiste configuratie van account machtigingen en andere gegevens nodig, zodat de runbooks kunnen worden ondertekend op de Linux Runbook Worker.

#### <a name="resolution"></a>Oplossing

* Zorg ervoor dat de Hybrid Runbook Worker het uitvoer bare bestand GnuPG (GPG) op de computer heeft.

* Controleer de configuratie van het **nxautomationuser** -account in het **sudo** -bestand. Zie [Runbooks uitvoeren op een Hybrid Runbook worker](../automation-hrw-run-runbooks.md).

### <a name="scenario-log-analytics-agent-for-linux-isnt-running"></a><a name="oms-agent-not-running"></a>Scenario: Log Analytics agent voor Linux is niet actief

#### <a name="issue"></a>Probleem

De Log Analytics-agent voor Linux wordt niet uitgevoerd.

#### <a name="cause"></a>Oorzaak

Als de agent niet wordt uitgevoerd, voor komt u dat de Linux-Hybrid Runbook Worker communiceert met Azure Automation. De agent kan om verschillende redenen niet worden uitgevoerd.

#### <a name="resolution"></a>Oplossing

 Controleer of de agent wordt uitgevoerd door de opdracht in te voeren `ps -ef | grep python` . De uitvoer ziet er als volgt uit. De python-processen met het **nxautomation** -gebruikers account. Als de functie Azure Automation niet is ingeschakeld, worden er geen van de volgende processen uitgevoerd.

```bash
nxautom+   8567      1  0 14:45 ?        00:00:00 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/main.py /var/opt/microsoft/omsagent/state/automationworker/oms.conf rworkspace:<workspaceId> <Linux hybrid worker version>
nxautom+   8593      1  0 14:45 ?        00:00:02 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/hybridworker.py /var/opt/microsoft/omsagent/state/automationworker/worker.conf managed rworkspace:<workspaceId> rversion:<Linux hybrid worker version>
nxautom+   8595      1  0 14:45 ?        00:00:02 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/hybridworker.py /var/opt/microsoft/omsagent/<workspaceId>/state/automationworker/diy/worker.conf managed rworkspace:<workspaceId> rversion:<Linux hybrid worker version>
```

De volgende lijst bevat de processen die zijn gestart voor een Linux-Hybrid Runbook Worker. Ze bevinden zich allemaal in de/var/opt/Microsoft/omsagent/State/automationworker/-map.

* **OMS. conf**: het worker-proces. Het wordt direct vanuit DSC gestart.
* **Worker. conf**: het automatisch geregistreerde Hybrid worker-proces. Het is gestart door de worker Manager. Dit proces wordt gebruikt door Updatebeheer en is transparant voor de gebruiker. Dit proces is niet aanwezig als Updatebeheer niet is ingeschakeld op de computer.
* **zelf/Worker. conf**: het Hybrid worker-zelf-werk proces. Het zelf Hybrid worker-werk proces wordt gebruikt om gebruikers runbooks uit te voeren op de Hybrid Runbook Worker. Dit verschilt alleen van het automatisch geregistreerde Hybrid worker-proces in de sleutel Details waarvoor een andere configuratie wordt gebruikt. Dit proces is niet aanwezig als Azure Automation is uitgeschakeld en de zelf Linux-Hybrid Worker niet is geregistreerd.

Als de agent niet wordt uitgevoerd, voert u de volgende opdracht uit om de service te starten: `sudo /opt/microsoft/omsagent/bin/service_control restart` .

### <a name="scenario-the-specified-class-doesnt-exist"></a><a name="class-does-not-exist"></a>Scenario: de opgegeven klasse bestaat niet

Als u het fout bericht `The specified class does not exist..` in **/var/opt/Microsoft/omsconfig/omsconfig.log** ziet, moet de log Analytics-agent voor Linux worden bijgewerkt. Voer de volgende opdracht uit om de agent opnieuw te installeren.

```bash
wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <WorkspaceID> -s <WorkspaceKey>
```

## <a name="windows"></a>Windows

De Windows-Hybrid Runbook Worker is afhankelijk van de [log Analytics-agent voor Windows](../../azure-monitor/platform/log-analytics-agent.md) om te communiceren met uw Automation-account om de werk nemer te registreren, Runbook-taken te ontvangen en de status van het rapport. Als de registratie van de werk nemer mislukt, bevat deze sectie enkele mogelijke oorzaken.

### <a name="scenario-the-log-analytics-agent-for-windows-isnt-running"></a><a name="mma-not-running"></a>Scenario: de Log Analytics-agent voor Windows wordt niet uitgevoerd

#### <a name="issue"></a>Probleem

De `healthservice` wordt niet uitgevoerd op de Hybrid Runbook worker computer.

#### <a name="cause"></a>Oorzaak

Als de Log Analytics voor de Windows-service niet wordt uitgevoerd, kan de Hybrid Runbook Worker niet communiceren met Azure Automation.

#### <a name="resolution"></a>Oplossing

Controleer of de agent wordt uitgevoerd door de volgende opdracht in te voeren in Power shell: `Get-Service healthservice` . Als de service is gestopt, voert u de volgende opdracht in Power shell in om de service te starten: `Start-Service healthservice` .

### <a name="scenario-event-4502-in-the-operations-manager-log"></a><a name="event-4502"></a>Scenario: gebeurtenis 4502 in het Operations Manager-logboek

#### <a name="issue"></a>Probleem

In het gebeurtenis logboek van de **toepassing en het service servicelogboeken\operations-beheer** ziet u gebeurtenis 4502 en een gebeurtenis bericht dat bevat `Microsoft.EnterpriseManagement.HealthService.AzureAutomation.HybridAgent` met de volgende beschrijving:<br>`The certificate presented by the service \<wsid\>.oms.opinsights.azure.com was not issued by a certificate authority used for Microsoft services. Please contact your network administrator to see if they are running a proxy that intercepts TLS/SSL communication.`

#### <a name="cause"></a>Oorzaak

Dit probleem kan worden veroorzaakt door uw proxy of netwerk firewall die communicatie met Microsoft Azure blokkeert. Controleer of de computer uitgaande toegang heeft tot **\* . Azure-Automation.net** op poort 443.

#### <a name="resolution"></a>Oplossing

Logboeken worden lokaal opgeslagen op elke Hybrid worker op C:\ProgramData\Microsoft\System Center\Orchestrator\7.2\SMA\Sandboxes. U kunt controleren of er waarschuwingen of fouten zijn in de gebeurtenis logboeken Logs\Microsoft-SMA\Operations en toepassings-en **service-Servicelogboeken\operations Manager** van de **toepassing en services** . In deze logboeken wordt een connectiviteit of een ander type probleem aangegeven die van invloed is op het inschakelen van de functie voor Azure Automation, of een probleem dat bij normale bewerkingen wordt aangetroffen. Zie problemen [met de log Analytics Windows-agent oplossen](../../azure-monitor/platform/agent-windows-troubleshoot.md)voor meer informatie over het oplossen van problemen met de log Analytics-agent.

Hybride werk rollen verzenden [runbook-uitvoer en-berichten](../automation-runbook-output-and-messages.md) naar Azure Automation op dezelfde manier als de runbook-taken die in de cloud worden uitgevoerd, uitvoer en berichten verzenden. U kunt de uitgebreide en voortgangs stromen op dezelfde manier inschakelen als voor runbooks.

### <a name="scenario-orchestratorsandboxexe-cant-connect-to-microsoft-365-through-proxy"></a>Scenario: Orchestrator.Sandbox.exe kan geen verbinding maken met Microsoft 365 via proxy

#### <a name="issue"></a>Probleem

Een script dat wordt uitgevoerd op een Windows-Hybrid Runbook Worker kan niet op de verwachte manier verbinding maken met Microsoft 365 op een Orchestrator sandbox. Het script maakt gebruik van [Connect-MsolService](/powershell/module/msonline/connect-msolservice) voor de verbinding. 

Als u **Orchestrator.Sandbox.exe.config** wijzigt om de proxy en de lijst overs laan in te stellen, wordt de sandbox nog steeds niet goed verbonden. Een **Powershell_ise.exe.config** -bestand met dezelfde proxy en de lijst instellingen overs Laan lijkt te werken zoals verwacht. Service Management Automation-Logboeken (SMA) en Power shell-logboeken bevatten geen informatie over de proxy.

#### <a name="cause"></a>Oorzaak

De verbinding met Active Directory Federation Services (AD FS) op de server kan de proxy niet overs Laan. Houd er rekening mee dat een Power shell-sandbox als de geregistreerde gebruiker wordt uitgevoerd. Een Orchestrator sandbox is echter sterk aangepast en kan de **Orchestrator.Sandbox.exe.config** Bestands instellingen negeren. Het heeft speciale code voor het verwerken van de computer-of Log Analytics agent proxy-instellingen, maar niet voor het afhandelen van andere aangepaste proxy-instellingen. 

#### <a name="resolution"></a>Oplossing

U kunt het probleem voor de Orchestrator sandbox oplossen door uw script te migreren om de Azure Active Directory modules te gebruiken in plaats van de MSOnline-module voor Power shell-cmdlets. Zie [migreren van Orchestrator naar Azure Automation (bèta)](../automation-orchestrator-migration.md)voor meer informatie.

Als u de MSOnline-module-cmdlets wilt blijven gebruiken, wijzigt u het script om de [opdracht invoke-Command](/powershell/module/microsoft.powershell.core/invoke-command)te gebruiken. Geef waarden op voor `ComputerName` de `Credential` para meters en. 

```powershell
$Credential = Get-AutomationPSCredential -Name MyProxyAccessibleCredential
Invoke-Command -ComputerName $env:COMPUTERNAME -Credential $Credential 
{ Connect-MsolService … }
```

Met deze code wijziging wordt een volledig nieuwe Power shell-sessie gestart in de context van de opgegeven referenties. Het moet het verkeer mogelijk maken om een proxy server te laten stromen die de actieve gebruiker verifieert.

>[!NOTE]
>Deze oplossing maakt het niet meer nodig om het configuratie bestand van de sandbox te bewerken. Zelfs als u het configuratie bestand met uw script gebruikt, wordt het bestand elke keer dat de Hybrid Runbook Worker agent wordt bijgewerkt, gewist.

### <a name="scenario-hybrid-runbook-worker-not-reporting"></a><a name="corrupt-cache"></a>Scenario: Hybrid Runbook Worker niet rapporteren

#### <a name="issue"></a>Probleem

Uw Hybrid Runbook Worker machine wordt uitgevoerd, maar u ziet geen heartbeat-gegevens voor de machine in de werk ruimte.

In het volgende voor beeld ziet u de computers in een werk ruimte en de laatste heartbeat:

```loganalytics
// Last heartbeat of each computer
Heartbeat
| summarize arg_max(TimeGenerated, *) by Computer
```

#### <a name="cause"></a>Oorzaak

Dit probleem kan worden veroorzaakt door een beschadigde cache op het Hybrid Runbook Worker.

#### <a name="resolution"></a>Oplossing

Om dit probleem op te lossen, meldt u zich aan bij de Hybrid Runbook Worker en voert u het volgende script uit. Met dit script wordt de Log Analytics agent voor Windows gestopt, wordt de cache ervan verwijderd en wordt de service opnieuw gestart. Deze actie dwingt de Hybrid Runbook Worker de configuratie opnieuw te downloaden van Azure Automation.

```powershell
Stop-Service -Name HealthService

Remove-Item -Path 'C:\Program Files\Microsoft Monitoring Agent\Agent\Health Service State' -Recurse

Start-Service -Name HealthService
```

### <a name="scenario-you-cant-add-a-windows-hybrid-runbook-worker"></a><a name="already-registered"></a>Scenario: u kunt geen Windows-Hybrid Runbook Worker toevoegen

#### <a name="issue"></a>Probleem

Het volgende bericht wordt weer gegeven wanneer u probeert een Hybrid Runbook Worker toe te voegen met behulp van de `Add-HybridRunbookWorker` cmdlet:

```error
Machine is already registered
```

#### <a name="cause"></a>Oorzaak

Dit probleem kan optreden als de computer al is geregistreerd met een ander Automation-account of als u de Hybrid Runbook Worker opnieuw wilt toevoegen nadat u deze van een computer hebt verwijderd.

#### <a name="resolution"></a>Oplossing

U kunt dit probleem oplossen door de volgende register sleutel te verwijderen, opnieuw op te starten `HealthService` en de `Add-HybridRunbookWorker` cmdlet opnieuw te proberen.

`HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\HybridRunbookWorker`

### <a name="scenario-you-cant-add-a-linux-hybrid-runbook-worker"></a><a name="already-registered"></a>Scenario: u kunt geen Linux-Hybrid Runbook Worker toevoegen

#### <a name="issue"></a>Probleem

Het volgende bericht wordt weer gegeven wanneer u probeert een Hybrid Runbook Worker toe te voegen met behulp van het `sudo python /opt/microsoft/omsconfig/.../onboarding.py --register` python-script:

```error
Unable to register, an existing worker was found. Please deregister any existing worker and try again.
```

Daarnaast probeert u de registratie van een Hybrid Runbook Worker ongedaan te maken met behulp van het `sudo python /opt/microsoft/omsconfig/.../onboarding.py --deregister` python-script:

```error
Failed to deregister worker. [response_status=404]
```

#### <a name="cause"></a>Oorzaak

Dit probleem kan optreden als de computer al is geregistreerd met een ander Automation-account, als de Azure Hybrid Worker-groep is verwijderd of als u probeert de Hybrid Runbook Worker opnieuw toe te voegen nadat u deze van een computer hebt verwijderd.

#### <a name="resolution"></a>Oplossing

Ga als volgt te werk om het probleem op te lossen:

1. Verwijder de agent `sudo sh onboard_agent.sh --purge` .

1. Voer deze opdrachten uit:

   ```
   sudo mv -f /home/nxautomation/state/worker.conf /home/nxautomation/state/worker.conf_old
   sudo mv -f /home/nxautomation/state/worker_diy.crt /home/nxautomation/state/worker_diy.crt_old
   sudo mv -f /home/nxautomation/state/worker_diy.key /home/nxautomation/state/worker_diy.key_old
   ```

1. De agent opnieuw onboarden `sudo sh onboard_agent.sh -w <workspace id> -s <workspace key> -d opinsights.azure.com` .

1. Wacht totdat de map is `/opt/microsoft/omsconfig/modules/nxOMSAutomationWorker` gevuld.

1. Probeer het `sudo python /opt/microsoft/omsconfig/.../onboarding.py --register` python-script opnieuw uit.

## <a name="next-steps"></a>Volgende stappen

Als uw probleem hier niet wordt weer gegeven of u het probleem niet kunt oplossen, kunt u een van de volgende kanalen proberen voor aanvullende ondersteuning:

* Krijg antwoorden van Azure-experts via [Azure-forums](https://azure.microsoft.com/support/forums/).
* Maak verbinding met [@AzureSupport](https://twitter.com/azuresupport) , het officiële Microsoft Azure account voor het verbeteren van de gebruikers ervaring. Azure-ondersteuning verbindt de Azure-community met antwoorden, ondersteuning en experts.
* Een ondersteunings incident voor Azure. Ga naar de [ondersteunings site van Azure](https://azure.microsoft.com/support/options/)en selecteer **ondersteuning verkrijgen**.
