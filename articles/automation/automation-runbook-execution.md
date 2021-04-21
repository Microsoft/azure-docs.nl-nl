---
title: Uitvoering van runbooks in Azure Automation
description: Dit artikel bevat een overzicht van de verwerking van runbooks in Azure Automation.
services: automation
ms.subservice: process-automation
ms.date: 03/23/2021
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 0807b11adfc46b9c32a8f7bd36a2f7d4db519975
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107830516"
---
# <a name="runbook-execution-in-azure-automation"></a>Uitvoering van runbooks in Azure Automation

Met procesautomatisering in Azure Automation kunt u PowerShell, PowerShell Workflow en grafische runbooks maken en beheren. Zie [runbooks Azure Automation voor meer informatie.](automation-runbook-types.md) 

Automation voert uw runbooks uit op basis van de logica die erin is gedefinieerd. Als een runbook wordt onderbroken, wordt het aan het begin opnieuw opgestart. Voor dit gedrag moet u runbooks schrijven die ondersteuning bieden voor opnieuw opstarten als er tijdelijke problemen optreden.

Als u een runbook in Azure Automation, wordt er een taak gemaakt. Dit is één exemplaar van het runbook. Elke taak heeft toegang tot Azure-resources door verbinding te maken met uw Azure-abonnement. De taak heeft alleen toegang tot resources in uw datacenter als deze resources toegankelijk zijn vanuit de openbare cloud.

Azure Automation wijst een werker toe om elke taak uit te voeren tijdens het uitvoeren van het runbook. Hoewel werkmedewerkers worden gedeeld door veel Azure-accounts, worden taken uit verschillende Automation-accounts van elkaar geïsoleerd. U kunt niet bepalen welke werkwerkers uw taakaanvragen verwerken.

Wanneer u de lijst met runbooks in de Azure Portal, wordt de status weergegeven van elke taak die voor elk runbook is gestart. Azure Automation worden taaklogboeken maximaal 30 dagen op slaat.

In het volgende diagram ziet u de levenscyclus van een runbooktaak voor [PowerShell-runbooks,](automation-runbook-types.md#powershell-runbooks) [PowerShell Workflow-runbooks](automation-runbook-types.md#powershell-workflow-runbooks)en [grafische runbooks.](automation-runbook-types.md#graphical-runbooks)

![Taakstatussen - PowerShell Workflow](./media/automation-runbook-execution/job-statuses.png)

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="runbook-execution-environment"></a>Uitvoeringsomgeving voor runbook

Runbooks in Azure Automation kunnen worden uitgevoerd op een Azure-sandbox of een [Hybrid Runbook Worker](automation-hybrid-runbook-worker.md). 

Wanneer runbooks zijn ontworpen om te worden geverifieerd en uitgevoerd op resources in Azure, worden ze uitgevoerd in een Azure-sandbox. Dit is een gedeelde omgeving die door meerdere taken kan worden gebruikt. Taken die dezelfde sandbox gebruiken, zijn gebonden aan de resourcebeperkingen van de sandbox. De Azure-sandboxomgeving biedt geen ondersteuning voor interactieve bewerkingen. Het voorkomt toegang tot alle out-of-process COM-servers [](/windows/win32/wmisdk/wmi-architecture) en biedt geen ondersteuning voor het maken van WMI-aanroepen naar de Win32-provider in uw runbook.  Deze scenario's worden alleen ondersteund door het runbook uit te voeren op een Windows-Hybrid Runbook Worker.


U kunt ook een [Hybrid Runbook Worker](automation-hybrid-runbook-worker.md) om runbooks rechtstreeks uit te voeren op de computer die als host voor de rol en voor lokale resources in de omgeving wordt gebruikt. Azure Automation runbooks op en beheert ze en levert ze vervolgens aan een of meer toegewezen computers.

>[!NOTE]
>Als u wilt uitvoeren op een Linux-Hybrid Runbook Worker, moeten uw scripts zijn ondertekend en moet de werkmedewerker dienovereenkomstig worden geconfigureerd. Handtekeningvalidatie [moet ook worden uitgeschakeld.](automation-linux-hrw-install.md#turn-off-signature-validation)

De volgende tabel bevat een aantal runbookuitvoeringstaken met de aanbevolen uitvoeringsomgeving die voor elke taak wordt vermeld.

|Taak|Aanbeveling|Notities|
|---|---|---|
|Integreren met Azure-resources|Azure Sandbox|Verificatie wordt gehost in Azure en is eenvoudiger. Als u een Hybrid Runbook Worker op een virtuele Azure-VM gebruikt, kunt u [runbookverificatie gebruiken met beheerde identiteiten.](automation-hrw-run-runbooks.md#runbook-auth-managed-identities)|
|Optimale prestaties verkrijgen voor het beheren van Azure-resources|Azure Sandbox|Script wordt uitgevoerd in dezelfde omgeving, met minder latentie.|
|Operationele kosten minimaliseren|Azure Sandbox|Er is geen compute-overhead en er is geen VM nodig.|
|Langlopende script uitvoeren|Hybrid Runbook Worker|Azure-sandboxes hebben [resourcelimieten.](../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits)|
|Interactie met lokale services|Hybrid Runbook Worker|Rechtstreeks toegang tot de hostmachine of resources in andere cloudomgevingen of de on-premises omgeving. |
|Software en uitvoerbare bestanden van derden vereisen|Hybrid Runbook Worker|U beheert het besturingssysteem en kunt software installeren.|
|Een bestand of map bewaken met een runbook|Hybrid Runbook Worker|Gebruik een [Watcher-taak](automation-watchers-tutorial.md) op een Hybrid Runbook Worker.|
|Een resource-intensief script uitvoeren|Hybrid Runbook Worker| Azure-sandboxes hebben [resourcelimieten.](../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits)|
|Modules met specifieke vereisten gebruiken| Hybrid Runbook Worker|Een aantal voorbeelden:</br> WinSCP : afhankelijkheid van winscp.exe </br> IIS-beheer: afhankelijkheid van het inschakelen of beheren van IIS|
|Een module installeren met een installatieprogramma|Hybrid Runbook Worker|Modules voor sandbox moeten ondersteuning bieden voor kopiëren.|
|Runbooks of modules gebruiken waarvoor een andere .NET Framework versie dan 4.7.2 is vereist|Hybrid Runbook Worker|Azure-sandboxes ondersteunen .NET Framework versie 4.7.2 en upgraden naar een andere versie wordt niet ondersteund.|
|Scripts uitvoeren waarvoor verhoging van bevoegdheden is vereist|Hybrid Runbook Worker|Sandboxes staan geen verhoging van bevoegdheden toe. Met een Hybrid Runbook Worker kunt u UAC uitschakelen en [Invoke-Command gebruiken bij](/powershell/module/microsoft.powershell.core/invoke-command) het uitvoeren van de opdracht die verhoging vereist.|
|Scripts uitvoeren waarvoor toegang tot Windows Management Instrumentation (WMI) is vereist|Hybrid Runbook Worker|Taken die worden uitgevoerd in sandboxes in de cloud, hebben geen toegang tot de WMI-provider. |

## <a name="temporary-storage-in-a-sandbox"></a>Tijdelijke opslag in een sandbox

Als u tijdelijke bestanden moet maken als onderdeel van uw runbooklogica, kunt u de map Temp (dat wil zeggen) in de Azure-sandbox gebruiken voor runbooks die worden uitgevoerd `$env:TEMP` in Azure. De enige beperking is dat u niet meer dan 1 GB schijfruimte kunt gebruiken. Dit is het quotum voor elke sandbox. Wanneer u met PowerShell-werkstromen werkt, kan dit scenario een probleem veroorzaken omdat PowerShell-werkstromen controlepunten gebruiken en het script opnieuw kan worden proberen in een andere sandbox.

Met de hybride sandbox kunt u gebruiken op `C:\temp` basis van de beschikbaarheid van opslag op een Hybrid Runbook Worker. Volgens de aanbevelingen voor azure-VM's [](../virtual-machines/managed-disks-overview.md#temporary-disk) moet u de tijdelijke schijf in Windows of Linux echter niet gebruiken voor gegevens die persistent moeten worden gemaakt.

## <a name="resources"></a>Resources

Uw runbooks moeten logica bevatten voor het omgaan met [resources,](/rest/api/resources/resources)bijvoorbeeld VM's, het netwerk en resources in het netwerk. Resources zijn gekoppeld aan een Azure-abonnement en runbooks hebben de juiste referenties nodig voor toegang tot een resource. Zie Resources verwerken voor een voorbeeld van het verwerken van resources in [een](manage-runbooks.md#handle-resources)runbook.

## <a name="security"></a>Beveiliging

Azure Automation maakt gebruik van [de Azure Security Center (ASC)](../security-center/security-center-introduction.md) om uw resources te beveiliging te bieden en beveiligingsrisico's in Linux-systemen te detecteren. Er wordt beveiliging geboden voor uw workloads, ongeacht of de resources zich in Azure of niet hebben. Zie [Inleiding tot verificatie in Azure Automation.](automation-security-overview.md)

ASC legt beperkingen op aan gebruikers die scripts( ondertekend of niet-ondertekend) kunnen uitvoeren op een VM. Als u een gebruiker bent met hoofdtoegang tot een virtuele machine, moet u de machine expliciet configureren met een digitale handtekening of uitschakelen. Anders kunt u alleen een script uitvoeren om besturingssysteemupdates toe te passen nadat u een Automation-account hebt gemaakt en de juiste functie hebt inschakelen.

## <a name="subscriptions"></a>Abonnementen

Een [Azure-abonnement](/office365/enterprise/subscriptions-licenses-accounts-and-tenants-for-microsoft-cloud-offerings) is een overeenkomst met Microsoft voor het gebruik van een of meer cloudservices waarvoor u kosten in rekening wordt gebracht. Voor Azure Automation is elk abonnement gekoppeld aan een Azure Automation-account en kunt u meerdere [abonnementen](manage-runbooks.md#work-with-multiple-subscriptions) in het account maken.

## <a name="credentials"></a>Referenties

Voor een runbook zijn [de juiste referenties vereist](shared-resources/credentials.md) voor toegang tot elke resource, of dit nu voor Azure- of systemen van derden is. Deze referenties worden opgeslagen in Azure Automation, Key Vault, enzovoort.  

## <a name="azure-monitor"></a>Azure Monitor

Azure Automation maakt gebruik van [Azure Monitor](../azure-monitor/overview.md) voor het bewaken van de machinebewerkingen. Voor de bewerkingen zijn een Log Analytics-werkruimte en een [Log Analytics-agent vereist.](../azure-monitor/agents/log-analytics-agent.md)

### <a name="log-analytics-agent-for-windows"></a>Log Analytics-agent voor Windows

De [Log Analytics-agent voor Windows](../azure-monitor/agents/agent-windows.md) werkt met Azure Monitor voor het beheren van Windows-VM's en fysieke computers. De machines kunnen worden uitgevoerd in Azure of in een niet-Azure-omgeving, zoals een lokaal datacenter.

>[!NOTE]
>De Log Analytics-agent voor Windows werd voorheen de MMA (Microsoft Monitoring Agent) genoemd.

### <a name="log-analytics-agent-for-linux"></a>Log Analytics-agent voor Linux

De [Log Analytics-agent voor Linux](../azure-monitor/agents/agent-linux.md) werkt op dezelfde manier als de agent voor Windows, maar verbindt Linux-computers met Azure Monitor. De agent wordt geïnstalleerd met **een nxautomation-gebruikersaccount** waarmee opdrachten kunnen worden uitgevoerd waarvoor hoofdmachtigingen zijn vereist, bijvoorbeeld op een Hybrid Runbook Worker. Het **nxautomation-account** is een systeemaccount waarvoor geen wachtwoord is vereist.

Het **nxautomation-account** met de bijbehorende sudo-machtigingen moet aanwezig zijn tijdens de installatie [van een Linux Hybrid Runbook Worker.](automation-linux-hrw-install.md) Als u de werkmedewerker probeert te installeren en het account niet aanwezig is of niet de juiste machtigingen heeft, mislukt de installatie.

U mag de machtigingen van de map of het `sudoers.d` eigendom ervan niet wijzigen. Sudo-machtiging is vereist voor **het nxautomation-account** en de machtigingen mogen niet worden verwijderd. Als u dit beperkt tot bepaalde mappen of opdrachten, kan dit leiden tot een wijziging die in de weg staat.

De logboeken die beschikbaar zijn voor de Log Analytics-agent en het **nxautomation-account** zijn:

* /var/opt/microsoft/omsagent/log/omsagent.log - Log Analytics-agentlogboek
* /var/opt/microsoft/omsagent/run/automationworker/worker.log - Automation-werklogboek

>[!NOTE]
>De **nxautomation-gebruiker** die is ingeschakeld als onderdeel van Updatebeheer alleen ondertekende runbooks uitvoert.

## <a name="runbook-permissions"></a>Runbookmachtigingen

Een runbook heeft machtigingen nodig voor verificatie bij Azure, via referenties. Zie [Azure Automation overzicht van verificatie.](automation-security-overview.md)

## <a name="modules"></a>Modules

Azure Automation ondersteunt een aantal standaardmodules, waaronder een aantal AzureRM-modules (AzureRM.Automation) en een module met verschillende interne cmdlets. Ook worden installeerbare modules ondersteund, waaronder de Az-modules (Az.Automation), die momenteel worden gebruikt in plaats van AzureRM-modules. Zie Modules beheren in Azure Automation voor meer informatie over de modules die beschikbaar zijn voor uw runbooks [en DSC-configuraties.](shared-resources/modules.md)

## <a name="certificates"></a>Certificaten

Azure Automation maakt [gebruik van certificaten](shared-resources/certificates.md) voor verificatie bij Azure of voegt deze toe aan Azure- of externe resources. De certificaten worden veilig opgeslagen voor toegang door runbooks en DSC-configuraties.

Uw runbooks kunnen zelf-ondertekende certificaten gebruiken, die niet zijn ondertekend door een certificeringsinstantie (CA). Zie [Een nieuw certificaat maken.](shared-resources/certificates.md#create-a-new-certificate)

## <a name="jobs"></a>Taken

Azure Automation ondersteunt een omgeving om taken uit te voeren vanuit hetzelfde Automation-account. Eén runbook kan veel taken tegelijk uitvoeren. Hoe meer taken u tegelijkertijd kunt uitvoeren, hoe vaker ze naar dezelfde sandbox kunnen worden verzonden. 

Taken die in hetzelfde sandboxproces worden uitgevoerd, kunnen van invloed zijn op elkaar. Een voorbeeld hiervan is het uitvoeren van de cmdlet [Disconnect-AzAccount.](/powershell/module/az.accounts/disconnect-azaccount) Als deze cmdlet wordt uitgevoerd, wordt elke runbook-taak in het gedeelde sandboxproces losgekoppeld. Zie Gelijktijdige taken voorkomen voor een voorbeeld van het werken [met dit scenario.](manage-runbooks.md#prevent-concurrent-jobs)

>[!NOTE]
>PowerShell-taken die zijn gestart vanuit een runbook dat wordt uitgevoerd in een Azure-sandbox, worden mogelijk niet uitgevoerd in de volledige [PowerShell-taalmodus.](/powershell/module/microsoft.powershell.core/about/about_language_modes)

### <a name="job-statuses"></a>Taakstatussen

In de volgende tabel worden de statussen beschreven die voor een taak mogelijk zijn. U kunt een statusoverzicht weergeven voor alle runbooktaken of inzoomen op details van een specifieke runbook-taak in de Azure Portal. U kunt ook integratie met uw Log Analytics-werkruimte configureren om de runbook-taakstatus en taakstromen door te geven. Zie Taakstatus en taakstromen van Automation doorsturen naar Azure Monitor logboeken voor meer informatie over het integreren [Azure Monitor logboeken.](automation-manage-send-joblogs-log-analytics.md) Zie ook [Taakstatussen verkrijgen](manage-runbooks.md#obtain-job-statuses) voor een voorbeeld van het werken met statussen in een runbook.

| Status | Beschrijving |
|:--- |:--- |
| Activeren |De taak wordt geactiveerd. |
| Voltooid |De taak is voltooid. |
| Mislukt |Het compileren van een grafisch of PowerShell Workflow-runbook is mislukt. Een PowerShell-runbook kan niet worden starten of de taak heeft een uitzondering. Zie [Azure Automation runbooktypen](automation-runbook-types.md).|
| Mislukt, wachten op resources |De taak is mislukt omdat de limiet voor [het](#fair-share) delen van eerlijk verkeer drie keer is bereikt en elke keer vanaf hetzelfde controlepunt of vanaf het begin van het runbook is gestart. |
| In wachtrij |De taak wacht tot resources op een Automation-werkster beschikbaar zijn, zodat deze kan worden gestart. |
| Hervatten |Het systeem hervat de taak nadat deze is opgeschort. |
| Wordt uitgevoerd |De taak wordt uitgevoerd. |
| Actief, wachten op resources |De taak is verwijderd omdat de limiet voor het delen van eerlijk delen is bereikt. Het wordt binnenkort hervat vanaf het laatste controlepunt. |
| Starten |De taak is toegewezen aan een werkmedewerker en het systeem start deze. |
| Gestopt |De gebruiker heeft de taak gestopt voordat deze is voltooid. |
| Stoppen |Het systeem stopt de taak. |
| Onderbroken |Is alleen [van toepassing op grafische runbooks en PowerShell Workflow-runbooks.](automation-runbook-types.md) De taak is door de gebruiker, door het systeem of door een opdracht in het runbook onderbroken. Als een runbook geen controlepunt heeft, begint het vanaf het begin. Als het een controlepunt heeft, kan het opnieuw starten en hervatten vanaf het laatste controlepunt. Het systeem schort het runbook alleen op wanneer er een uitzondering optreedt. De variabele is standaard ingesteld op Doorgaan, waarmee wordt aangegeven dat `ErrorActionPreference` de taak bij een fout blijft worden uitgevoerd. Als de voorkeursvariabele is ingesteld op Stoppen, wordt de taak bij een fout tijdelijk gestopt.  |
| Onderbreken |Is alleen [van toepassing op grafische runbooks en PowerShell Workflow-runbooks.](automation-runbook-types.md) Het systeem probeert de taak op verzoek van de gebruiker te schorsen. Runbook moet het volgende controlepunt bereiken voordat de taak kan worden onderbroken. Als het laatste controlepunt al is geslaagd, wordt het voltooid voordat het kan worden opgeschort. |

## <a name="activity-logging"></a>Logboekregistratie van activiteiten

Uitvoering van runbooks in Azure Automation schrijft details in een activiteitenlogboek voor het Automation-account. Zie Details ophalen uit activiteitenlogboek voor meer informatie over het gebruik [van het logboek.](manage-runbooks.md#retrieve-details-from-activity-log)

## <a name="exceptions"></a>Uitzonderingen

In deze sectie worden enkele manieren beschreven voor het afhandelen van uitzonderingen of onregelmatige problemen in uw runbooks. Een voorbeeld is een WebSocket-uitzondering. Juiste afhandeling van uitzonderingen voorkomt dat tijdelijke netwerkfouten ervoor zorgen dat uw runbooks mislukken.

### <a name="erroractionpreference"></a>ErrorActionPreference

De [variabele ErrorActionPreference](/powershell/module/microsoft.powershell.core/about/about_preference_variables#erroractionpreference) bepaalt hoe PowerShell reageert op een niet-beëindigingsfout. Het beëindigen van fouten wordt altijd beëindigd en wordt niet beïnvloed door `ErrorActionPreference` .

Wanneer het runbook gebruikmaakt van , wordt het runbook niet meer uitgevoerd door een normaal niet-af te ronden fout, zoals van de `ErrorActionPreference` `PathNotFound` cmdlet [Get-ChildItem.](/powershell/module/microsoft.powershell.management/get-childitem) In het volgende voorbeeld ziet u het gebruik van `ErrorActionPreference` . De laatste [write-output opdracht](/powershell/module/microsoft.powershell.utility/write-output) wordt nooit uitgevoerd, als het script stopt.

```powershell-interactive
$ErrorActionPreference = 'Stop'
Get-ChildItem -path nofile.txt
Write-Output "This message will not show"
```

### <a name="try-catch-finally"></a>Catch tot slot proberen

[Try Catch Finally wordt](/powershell/module/microsoft.powershell.core/about/about_try_catch_finally) gebruikt in PowerShell-scripts om beëindigingsfouten af te handelen. Het script kan dit mechanisme gebruiken om specifieke uitzonderingen of algemene uitzonderingen te ondervangen. De instructie moet worden gebruikt om fouten bij te `catch` houden of af te handelen. In het volgende voorbeeld wordt geprobeerd een bestand te downloaden dat niet bestaat. De uitzondering wordt `System.Net.WebException` vangt en retourneert de laatste waarde voor een andere uitzondering.

```powershell-interactive
try
{
   $wc = new-object System.Net.WebClient
   $wc.DownloadFile("http://www.contoso.com/MyDoc.doc")
}
catch [System.Net.WebException]
{
    "Unable to download MyDoc.doc from http://www.contoso.com."
}
catch
{
    "An error occurred that could not be resolved."
}
```

### <a name="throw"></a>Gooien

[Throw](/powershell/module/microsoft.powershell.core/about/about_throw) kan worden gebruikt om een eindfout te genereren. Dit mechanisme kan nuttig zijn bij het definiëren van uw eigen logica in een runbook. Als het script voldoet aan een criterium dat het script moet stoppen, kan het de `throw` instructie gebruiken om te stoppen. In het volgende voorbeeld wordt deze instructie gebruikt om een vereiste functieparameter weer te geven.

```powershell-interactive
function Get-ContosoFiles
{
  param ($path = $(throw "The Path parameter is required."))
  Get-ChildItem -Path $path\*.txt -recurse
}
```

## <a name="errors"></a>Fouten

Uw runbooks moeten fouten verwerken. Azure Automation ondersteunt twee typen PowerShell-fouten: beëindigen en niet-beëindigen. 

Het beëindigen van fouten stopt de uitvoering van runbook wanneer ze optreden. Het runbook stopt met de taakstatus Mislukt.

Bij niet-beëindigingsfouten kan een script doorgaan, zelfs nadat deze zijn uitgevoerd. Een voorbeeld van een niet-beëindigingsfout is een fout die zich voordoet wanneer een runbook de cmdlet gebruikt met een pad dat `Get-ChildItem` niet bestaat. PowerShell ziet dat het pad niet bestaat, er een fout wordt weergegeven en gaat door naar de volgende map. Met de fout wordt in dit geval de status van de runbook-taak niet ingesteld op Mislukt en kan de taak zelfs worden voltooid. Als u wilt forceren dat een runbook stopt bij een niet-beëindigingsfout, kunt u op `ErrorAction Stop` de cmdlet gebruiken.

## <a name="calling-processes"></a>Processen aanroepen

Runbooks die worden uitgevoerd in Azure-sandboxes bieden geen ondersteuning voor aanroepprocessen, zoals uitvoerbare bestanden **(EXE-bestanden)** of subprocessen. De reden hiervoor is dat een Azure-sandbox een gedeeld proces is dat wordt uitgevoerd in een container die mogelijk geen toegang heeft tot alle onderliggende API's. Voor scenario's waarvoor software van derden of aanroepen naar subprocessen zijn vereist, moet u een runbook uitvoeren op [een Hybrid Runbook Worker](automation-hybrid-runbook-worker.md).

## <a name="device-and-application-characteristics"></a>Apparaat- en toepassingskenmerken

Runbooktaken in Azure-sandboxes hebben geen toegang tot apparaat- of toepassingskenmerken. De meest voorkomende API die wordt gebruikt voor het opvragen van metrische gegevens over prestaties in Windows is WMI, met enkele van de algemene metrische gegevens geheugen- en CPU-gebruik. Het maakt echter niet uit welke API wordt gebruikt, omdat taken die worden uitgevoerd in de cloud geen toegang hebben tot de Microsoft-implementatie van Web-Based Enterprise Management (WBEM). Dit platform is gebaseerd op de Common Information Model (CIM) en biedt de industriestandaarden voor het definiëren van apparaat- en toepassingskenmerken.

## <a name="webhooks"></a>Webhooks

Externe services, bijvoorbeeld Azure DevOps Services en GitHub, kunnen een runbook starten in Azure Automation. Voor dit type opstarten gebruikt de service een [webhook](automation-webhooks.md) via één HTTP-aanvraag. Met het gebruik van een webhook kunnen runbooks worden gestart zonder implementatie van een Azure Automation functie.

## <a name="shared-resources"></a><a name="fair-share"></a>Gedeelde resources

Om resources te delen tussen alle runbooks in de cloud, maakt Azure gebruik van een concept dat fair share wordt genoemd. Met behulp van fair share wordt een taak die langer dan drie uur is uitgevoerd, tijdelijk verwijderd of gestopt in Azure. Taken voor [PowerShell-runbooks](automation-runbook-types.md#powershell-runbooks) en [Python-runbooks](automation-runbook-types.md#python-runbooks) worden gestopt en niet opnieuw gestart, en de taakstatus wordt Gestopt.

Voor langlopende Azure Automation is het raadzaam om een Hybrid Runbook Worker. Hybrid Runbook Workers worden niet beperkt door een fair share en hebben geen beperking voor hoe lang een runbook kan worden uitgevoerd. De andere [taaklimieten gelden](../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits) voor zowel Azure-sandboxes als Hybrid Runbook Workers. Hoewel Hybrid Runbook Workers niet wordt beperkt door de limiet van drie uur voor fair share, moet u runbooks ontwikkelen die worden uitgevoerd op de werksters die ondersteuning bieden voor opnieuw opstarten vanuit onverwachte problemen met de lokale infrastructuur.

Een andere optie is om een runbook te optimaliseren met behulp van onderliggende runbooks. Uw runbook kan bijvoorbeeld op verschillende resources door dezelfde functie lopen, bijvoorbeeld met een databasebewerking voor verschillende databases. U kunt deze functie verplaatsen naar een onderliggend runbook en uw [runbook](automation-child-runbooks.md) deze laten aanroepen met [Start-AzAutomationRunbook](/powershell/module/az.automation/start-azautomationrunbook). Onderliggende runbooks worden parallel uitgevoerd in afzonderlijke processen.

Het gebruik van onderliggende runbooks vermindert de totale tijd die nodig is om het bovenliggende runbook te voltooien. Uw runbook kan de cmdlet [Get-AzAutomationJob](/powershell/module/az.automation/get-azautomationjob) gebruiken om de taakstatus voor een onderliggend runbook te controleren als het nog meer bewerkingen heeft nadat het onderliggende runbook is voltooid.

## <a name="next-steps"></a>Volgende stappen

* Zie Zelfstudie: Een PowerShell-runbook maken om aan de slag te gaan met [een PowerShell-runbook.](learn/automation-tutorial-runbook-textual-powershell.md)
* Zie Runbooks beheren in Azure Automation voor [meer Azure Automation.](manage-runbooks.md)
* Zie PowerShell Docs voor meer [informatie over PowerShell.](/powershell/scripting/overview)
* Zie [Az.Automation](/powershell/module/az.automation#automation) voor een naslagdocumentatie voor een PowerShell-cmdlet.
