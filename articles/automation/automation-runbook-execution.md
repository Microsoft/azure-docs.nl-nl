---
title: Uitvoering van runbooks in Azure Automation
description: Dit artikel bevat een overzicht van de verwerking van runbooks in Azure Automation.
services: automation
ms.subservice: process-automation
ms.date: 03/23/2021
ms.topic: conceptual
ms.openlocfilehash: 165c9ea721bec7fc7a1657f5dde5c19d9e254e20
ms.sourcegitcommit: ac035293291c3d2962cee270b33fca3628432fac
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2021
ms.locfileid: "104954340"
---
# <a name="runbook-execution-in-azure-automation"></a>Uitvoering van runbooks in Azure Automation

Proces automatisering in Azure Automation kunt u Power shell-, Power shell-werk stroom en grafische runbooks maken en beheren. Zie [Azure Automation runbooks](automation-runbook-types.md)voor meer informatie. 

Met Automation worden uw runbooks uitgevoerd op basis van de logica die hierin is gedefinieerd. Als een runbook wordt onderbroken, wordt het aan het begin opnieuw opgestart. Dit gedrag vereist dat u runbooks schrijft die ondersteuning bieden voor opnieuw opstarten als tijdelijke problemen optreden.

Als u een runbook in Azure Automation start, wordt er een taak gemaakt. Dit is één uitvoerings instantie van het runbook. Elke taak heeft toegang tot Azure-resources door verbinding te maken met uw Azure-abonnement. De taak kan alleen toegang krijgen tot resources in uw Data Center als deze bronnen toegankelijk zijn vanuit de open bare Cloud.

Azure Automation wijst een werk nemer toe om elke taak uit te voeren tijdens de uitvoering van het runbook. Terwijl werk rollen worden gedeeld door veel Azure-accounts, zijn taken van verschillende Automation-accounts van elkaar geïsoleerd. U kunt niet bepalen welke Worker-Services uw taak aanvragen.

Wanneer u de lijst met runbooks in de Azure Portal weergeeft, wordt de status weer gegeven van elke taak die voor elk runbook is gestart. Met Azure Automation worden taak logboeken Maxi maal 30 dagen opgeslagen.

In het volgende diagram ziet u de levens cyclus van een runbook-taak voor [Power shell-runbooks](automation-runbook-types.md#powershell-runbooks), [Power shell-werk stroom runbooks](automation-runbook-types.md#powershell-workflow-runbooks)en [grafische runbooks](automation-runbook-types.md#graphical-runbooks).

![Taak statussen-Power shell-werk stroom](./media/automation-runbook-execution/job-statuses.png)

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="runbook-execution-environment"></a>Runbook Execution Environment

Runbooks in Azure Automation kunnen worden uitgevoerd op een Azure sandbox of een [Hybrid Runbook worker](automation-hybrid-runbook-worker.md). 

Wanneer runbooks zijn ontworpen om te verifiëren en uit te voeren op resources in azure, worden ze uitgevoerd in een Azure-sandbox. Dit is een gedeelde omgeving die door meerdere taken kan worden gebruikt. Taken die gebruikmaken van dezelfde sandbox zijn gebonden aan de resource beperkingen van de sandbox. De Azure sandbox-omgeving biedt geen ondersteuning voor interactieve bewerkingen. Het voor komt dat er toegang wordt geboden tot alle out-of-process COM-servers en biedt geen ondersteuning voor het maken van [WMI-aanroepen](/windows/win32/wmisdk/wmi-architecture) naar de Win32-provider in uw runbook.  Deze scenario's worden alleen ondersteund door het runbook uit te voeren op een Windows-Hybrid Runbook Worker.


U kunt ook een [Hybrid Runbook worker](automation-hybrid-runbook-worker.md) gebruiken om runbooks rechtstreeks uit te voeren op de computer die de rol host en de lokale resources in de omgeving. Azure Automation runbooks opslaat en beheert en vervolgens aan een of meer toegewezen computers worden geleverd.

>[!NOTE]
>Als u wilt uitvoeren op een Linux-Hybrid Runbook Worker, moeten uw scripts zijn ondertekend en moet de werk nemer op de juiste wijze zijn geconfigureerd. De validatie van de [hand tekening moet ook worden uitgeschakeld](automation-linux-hrw-install.md#turn-off-signature-validation).

De volgende tabel bevat een aantal uitvoerings taken voor runbook met de aanbevolen uitvoerings omgeving die voor elk wordt vermeld.

|Taak|Aanbeveling|Notities|
|---|---|---|
|Integreren met Azure-resources|Azure sandbox|Verificatie is eenvoudiger in Azure. Als u een Hybrid Runbook Worker op een virtuele Azure-machine gebruikt, kunt u [Runbook-verificatie gebruiken met beheerde identiteiten](automation-hrw-run-runbooks.md#runbook-auth-managed-identities).|
|Optimale prestaties verkrijgen om Azure-resources te beheren|Azure sandbox|Script wordt uitgevoerd in dezelfde omgeving, met minder latentie.|
|Beperk operationele kosten|Azure sandbox|Er zijn geen berekenings overhead en er is geen virtuele machine nodig.|
|Langlopend script uitvoeren|Hybrid Runbook Worker|Azure-sandboxes hebben [resource limieten](../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits).|
|Interactie met lokale services|Hybrid Runbook Worker|Rechtstreeks toegang krijgen tot de hostcomputer of bronnen in andere Cloud omgevingen of de on-premises omgeving. |
|Software en uitvoer bare bestanden van derden vereisen|Hybrid Runbook Worker|U beheert het besturings systeem en kan software installeren.|
|Een bestand of map bewaken met een runbook|Hybrid Runbook Worker|Een [Watcher-taak](automation-watchers-tutorial.md) gebruiken op een Hybrid Runbook Worker.|
|Een resource-intensieve script uitvoeren|Hybrid Runbook Worker| Azure-sandboxes hebben [resource limieten](../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits).|
|Modules met specifieke vereisten gebruiken| Hybrid Runbook Worker|Een aantal voorbeelden:</br> WinSCP-afhankelijkheid op winscp.exe </br> IIS-beheer-afhankelijkheid van het in-of beheren van IIS|
|Een module installeren met een installatie programma|Hybrid Runbook Worker|Modules voor sandbox moeten kopiëren ondersteunen.|
|Runbooks of modules gebruiken waarvoor .NET Framework versie anders is dan 4.7.2|Hybrid Runbook Worker|Azure-sandboxes ondersteunen .NET Framework 4.7.2 en de upgrade naar een andere versie wordt niet ondersteund.|
|Scripts uitvoeren waarvoor uitbrei ding van bevoegdheden is vereist|Hybrid Runbook Worker|Sandboxes staan geen uitbrei ding van bevoegdheden toe. Met een Hybrid Runbook Worker kunt u UAC uitschakelen en [invoke-opdracht](/powershell/module/microsoft.powershell.core/invoke-command) gebruiken bij het uitvoeren van de opdracht die uitbrei ding vereist.|
|Scripts uitvoeren waarvoor toegang tot Windows Management Instrumentation is vereist (WMI)|Hybrid Runbook Worker|Taken die worden uitgevoerd in sandboxes in de Cloud hebben geen toegang tot de WMI-provider. |

## <a name="temporary-storage-in-a-sandbox"></a>Tijdelijke opslag in een sandbox

Als u tijdelijke bestanden wilt maken als onderdeel van uw runbook-logica, kunt u de tijdelijke map (dat wil zeggen `$env:TEMP` ) gebruiken in de Azure sandbox voor runbooks die worden uitgevoerd in Azure. De enige beperking is dat u niet meer dan 1 GB schijf ruimte kunt gebruiken. Dit is het quotum voor elke sandbox. Wanneer u werkt met Power shell-werk stromen, kan dit scenario een probleem veroorzaken omdat Power shell-werk stromen controle punten gebruiken en het script opnieuw kan worden geprobeerd in een andere sandbox.

Met de hybride sandbox kunt u gebruiken op `C:\temp` basis van de beschik baarheid van de opslag op een Hybrid Runbook Worker. Bij aanbevelingen van de Azure-VM moet u echter geen gebruik maken van de [tijdelijke schijf](../virtual-machines/managed-disks-overview.md#temporary-disk) in Windows of Linux voor gegevens die moeten worden bewaard.

## <a name="resources"></a>Resources

Uw runbooks moeten logica bevatten om te kunnen omgaan met [resources](/rest/api/resources/resources), bijvoorbeeld vm's, het netwerk en bronnen in het netwerk. Resources zijn gekoppeld aan een Azure-abonnement en runbooks hebben de juiste referenties nodig om toegang te krijgen tot bronnen. Zie [resources verwerken](manage-runbooks.md#handle-resources)voor een voor beeld van het verwerken van resources in een runbook.

## <a name="security"></a>Beveiliging

Azure Automation gebruikt de [Azure Security Center (ASC)](../security-center/security-center-introduction.md) voor de beveiliging van uw resources en het detecteren van inbreuken in Linux-systemen. Er wordt beveiliging gegeven in uw workloads, ongeacht of de resources zich in azure bevinden. Zie [Inleiding tot verificatie in azure Automation](automation-security-overview.md).

Met ASC worden beperkingen ingesteld voor gebruikers die scripts kunnen uitvoeren, hetzij ondertekend of niet ondertekend, op een virtuele machine. Als u een gebruiker bent met hoofd toegang tot een VM, moet u de computer expliciet configureren met een digitale hand tekening of uitschakelen. Anders kunt u alleen een script uitvoeren om updates van het besturings systeem toe te passen na het maken van een Automation-account en de juiste functie in te scha kelen.

## <a name="subscriptions"></a>Abonnementen

Een Azure- [abonnement](/office365/enterprise/subscriptions-licenses-accounts-and-tenants-for-microsoft-cloud-offerings) is een overeenkomst met micro soft voor het gebruik van een of meer Cloud Services waarvoor kosten in rekening worden gebracht. Voor Azure Automation is elk abonnement gekoppeld aan een Azure Automation-account en kunt u [meerdere abonnementen maken](manage-runbooks.md#work-with-multiple-subscriptions) in het account.

## <a name="credentials"></a>Referenties

Voor een runbook zijn de juiste [referenties](shared-resources/credentials.md) vereist om toegang te krijgen tot bronnen, ongeacht of het om Azure of systemen van derden gaat. Deze referenties worden opgeslagen in Azure Automation, Key Vault, enzovoort.  

## <a name="azure-monitor"></a>Azure Monitor

Azure Automation maakt gebruik van [Azure monitor](../azure-monitor/overview.md) voor het bewaken van de computer bewerkingen. Voor de bewerkingen is een Log Analytics-werk ruimte en een [log Analytics-agent](../azure-monitor/agents/log-analytics-agent.md)vereist.

### <a name="log-analytics-agent-for-windows"></a>Log Analytics-agent voor Windows

De [log Analytics-agent voor Windows](../azure-monitor/agents/agent-windows.md) werkt met Azure monitor voor het beheren van Windows-vm's en fysieke computers. De machines kunnen worden uitgevoerd in azure of in een niet-Azure-omgeving, zoals een lokaal Data Center.

>[!NOTE]
>De Log Analytics-agent voor Windows was voorheen bekend als micro soft Monitoring Agent (MMA).

### <a name="log-analytics-agent-for-linux"></a>Log Analytics-agent voor Linux

De [log Analytics-agent voor Linux](../azure-monitor/agents/agent-linux.md) werkt op dezelfde manier als de agent voor Windows, maar verbindt Linux-computers met Azure monitor. De agent wordt geïnstalleerd met een **nxautomation** -gebruikers account waarmee opdrachten kunnen worden uitgevoerd, bijvoorbeeld op een Hybrid Runbook Worker. Het **nxautomation** -account is een systeem account waarvoor geen wacht woord is vereist.

Het **nxautomation** -account met de bijbehorende sudo-machtigingen moet aanwezig zijn tijdens de [installatie van een Linux Hybrid Runbook worker](automation-linux-hrw-install.md). Als u de werk nemer probeert te installeren en het account niet aanwezig is of niet de juiste machtigingen heeft, mislukt de installatie.

U moet de machtigingen van de `sudoers.d` map of het eigendom niet wijzigen. De sudo-machtiging is vereist voor het **nxautomation** -account en de machtigingen moeten niet worden verwijderd. Als u dit beperkt tot bepaalde mappen of opdrachten, kan dit leiden tot een belang rijke wijziging.

De logboeken die beschikbaar zijn voor de Log Analytics-agent en het **nxautomation** -account zijn:

* /var/opt/Microsoft/omsagent/log/omsagent.log-Log Analytics Agent-logboek
* /var/opt/Microsoft/omsagent/run/automationworker/Worker.log-Automation-werk logboek

>[!NOTE]
>De **nxautomation** -gebruiker die is ingeschakeld als onderdeel van updatebeheer, voert alleen ondertekende runbooks uit.

## <a name="runbook-permissions"></a>Runbookmachtigingen

Een runbook heeft machtigingen nodig voor de verificatie van Azure, via referenties. Zie [Azure Automation verificatie-overzicht](automation-security-overview.md).

## <a name="modules"></a>Modules

Azure Automation ondersteunt een aantal standaard modules, waaronder sommige AzureRM-modules (AzureRM. Automation) en een module met meerdere interne cmdlets. Ook worden Installeer bare modules ondersteund, met inbegrip van de AZ-modules (AZ. Automation), die momenteel worden gebruikt in de voor keur voor AzureRM-modules. Zie [modules beheren in azure Automation](shared-resources/modules.md)voor meer informatie over de modules die beschikbaar zijn voor RUNBOOKS en DSC-configuraties.

## <a name="certificates"></a>Certificaten

Azure Automation gebruikt [certificaten](shared-resources/certificates.md) voor verificatie voor Azure of voegt deze toe aan Azure of bronnen van derden. De certificaten worden veilig opgeslagen voor toegang door runbooks en DSC-configuraties.

Uw runbooks kunnen zelfondertekende certificaten gebruiken die niet zijn ondertekend door een certificerings instantie (CA). Zie [een nieuw certificaat maken](shared-resources/certificates.md#create-a-new-certificate).

## <a name="jobs"></a>Taken

Azure Automation ondersteunt een omgeving om taken uit te voeren vanuit hetzelfde Automation-account. Eén runbook kan meerdere taken tegelijk uitvoeren. Hoe meer taken u tegelijkertijd uitvoert, hoe vaak deze naar dezelfde sandbox kunnen worden verzonden. 

Taken die worden uitgevoerd in hetzelfde sandbox-proces, kunnen van invloed zijn op elkaar. De cmdlet [Disconnect-AzAccount](/powershell/module/az.accounts/disconnect-azaccount) wordt uitgevoerd op een voor beeld. Uitvoering van deze cmdlet verbreekt de verbinding van elke runbook-taak in het gedeelde sandbox-proces. Zie voor een voor beeld van het werken met dit scenario [gelijktijdige taken voor komen](manage-runbooks.md#prevent-concurrent-jobs).

>[!NOTE]
>Power shell-taken die zijn gestart vanuit een runbook dat wordt uitgevoerd in een Azure-sandbox, worden mogelijk niet uitgevoerd in de volledige [Power shell-taal modus](/powershell/module/microsoft.powershell.core/about/about_language_modes).

### <a name="job-statuses"></a>Taak status

De volgende tabel beschrijft de statussen die mogelijk zijn voor een taak. U kunt een status overzicht weer geven voor alle runbook-taken of Details van een specifieke runbook-taak in de Azure Portal. U kunt ook integratie met uw Log Analytics-werk ruimte configureren om de taak status en taak stromen van een runbook door te sturen. Zie voor meer informatie over het integreren met Azure Monitor-logboeken [taak status door sturen van automatisering naar Azure monitor-logboeken](automation-manage-send-joblogs-log-analytics.md). Zie ook [taak statussen verkrijgen](manage-runbooks.md#obtain-job-statuses) voor een voor beeld van het werken met statussen in een runbook.

| Status | Beschrijving |
|:--- |:--- |
| Activeren |De taak wordt geactiveerd. |
| Voltooid |De taak is voltooid. |
| Mislukt |Het compileren van een grafisch of Power shell-werk stroom runbook is mislukt. Een Power shell-runbook kan niet worden gestart of er is een uitzonde ring opgetreden in de taak. Zie [Azure Automation typen runbook](automation-runbook-types.md).|
| Mislukt, wachten op resources |De taak is mislukt omdat de limiet voor de [billijke share](#fair-share) drie keer is bereikt en vanaf hetzelfde controle punt of vanaf het begin van het runbook elke keer is gestart. |
| In wachtrij |Er wordt gewacht tot resources op een Automation-werk nemer beschikbaar zijn, zodat deze kunnen worden gestart. |
| Hervatten |Het systeem hervat de taak nadat deze is onderbroken. |
| Wordt uitgevoerd |De taak wordt uitgevoerd. |
| Uitvoeren, wachten op resources |De taak is uit het geheugen verwijderd omdat de limiet voor de billijke share is bereikt. Het wordt binnenkort hervat vanaf het laatste controle punt. |
| Starten |De taak is toegewezen aan een werk nemer en het systeem wordt gestart. |
| Gestopt |De gebruiker heeft de taak gestopt voordat deze is voltooid. |
| Stoppen |Het systeem stopt de taak. |
| Onderbroken |Is alleen van toepassing op [grafische en Power shell-werk stroom-runbooks](automation-runbook-types.md) . De taak is door de gebruiker, door het systeem of door een opdracht in het runbook onderbroken. Als een runbook geen controle punt heeft, begint het vanaf het begin. Als er een controle punt is, kan het opnieuw worden gestart en wordt hervat vanaf het laatste controle punt. Het systeem onderbreekt alleen het runbook wanneer er een uitzonde ring optreedt. De `ErrorActionPreference` variabele wordt standaard ingesteld om door te gaan, wat aangeeft dat de taak wordt uitgevoerd op een fout. Als de voorkeurs variabele is ingesteld op Stop, wordt een fout door de taak onderbroken.  |
| Onderbreken |Is alleen van toepassing op [grafische en Power shell-werk stroom-runbooks](automation-runbook-types.md) . Het systeem probeert de taak op verzoek van de gebruiker te onderbreken. Runbook moet het volgende controlepunt bereiken voordat de taak kan worden onderbroken. Als het laatste controle punt al is door gegeven, wordt het voltooid voordat het kan worden onderbroken. |

## <a name="activity-logging"></a>Logboekregistratie van activiteiten

Als runbooks in Azure Automation worden uitgevoerd, worden gegevens in een activiteiten logboek voor het Automation-account geschreven. Zie [Details ophalen uit het activiteiten logboek](manage-runbooks.md#retrieve-details-from-activity-log)voor meer informatie over het gebruik van het logboek.

## <a name="exceptions"></a>Uitzonderingen

In deze sectie worden enkele manieren beschreven voor het afhandelen van uitzonde ringen of onregelmatige problemen in uw runbooks. Een voor beeld is een WebSocket-uitzonde ring. Door de verwerking van uitzonde ringen te corrigeren, kunnen tijdelijke netwerk fouten ertoe leiden dat uw runbooks mislukken.

### <a name="erroractionpreference"></a>ErrorActionPreference

De variabele [ErrorActionPreference](/powershell/module/microsoft.powershell.core/about/about_preference_variables#erroractionpreference) bepaalt hoe Power shell reageert op een niet-afsluit fout. Afsluit fouten worden altijd beëindigd en worden niet beïnvloed door `ErrorActionPreference` .

Wanneer het runbook wordt gebruikt `ErrorActionPreference` , wordt een normaal niet-afsluit fout, zoals `PathNotFound` van de cmdlet [Get-Child item](/powershell/module/microsoft.powershell.management/get-childitem) , het runbook gestopt. In het volgende voor beeld ziet u het gebruik van `ErrorActionPreference` . De laatste opdracht voor [Write-output](/powershell/module/microsoft.powershell.utility/write-output) wordt nooit uitgevoerd, omdat het script stopt.

```powershell-interactive
$ErrorActionPreference = 'Stop'
Get-ChildItem -path nofile.txt
Write-Output "This message will not show"
```

### <a name="try-catch-finally"></a>Probeer catch ten slotte

[Try catch finally](/powershell/module/microsoft.powershell.core/about/about_try_catch_finally) wordt gebruikt in Power shell-scripts voor het afhandelen van afsluit fouten. Het script kan dit mechanisme gebruiken om specifieke uitzonde ringen of algemene uitzonde ringen te ondervangen. De `catch` instructie moet worden gebruikt om fouten bij te houden of te proberen af te handelen. In het volgende voor beeld wordt geprobeerd een bestand te downloaden dat niet bestaat. De `System.Net.WebException` uitzonde ring wordt onderschept en de laatste waarde voor een andere uitzonde ring wordt geretourneerd.

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

### <a name="throw"></a>Sprong

[Throw](/powershell/module/microsoft.powershell.core/about/about_throw) kan worden gebruikt om een afsluit fout te genereren. Dit mechanisme kan nuttig zijn bij het definiëren van uw eigen logica in een runbook. Als het script voldoet aan een criterium waardoor het wordt gestopt, kan het de `throw` instructie gebruiken om te stoppen. In het volgende voor beeld wordt deze instructie gebruikt om een vereiste functie parameter weer te geven.

```powershell-interactive
function Get-ContosoFiles
{
  param ($path = $(throw "The Path parameter is required."))
  Get-ChildItem -Path $path\*.txt -recurse
}
```

## <a name="errors"></a>Fouten

Uw runbooks moeten fouten afhandelen. Azure Automation ondersteunt twee typen Power shell-fouten, beëindigen en niet-beëindigen. 

Bij het beëindigen van fouten wordt de uitvoering van het runbook gestopt wanneer deze zich voordoen. Het runbook stopt met de taak status mislukt.

Met niet-afsluit fouten kan een script worden voortgezet, zelfs nadat het is uitgevoerd. Een voor beeld van een niet-afsluit fout is een van de volgende situaties wanneer een runbook gebruikmaakt `Get-ChildItem` van de cmdlet met een pad dat niet bestaat. Power shell ziet dat het pad niet bestaat, een fout genereert en naar de volgende map gaat. Met de fout in dit geval wordt de status van de runbook-taak niet ingesteld op mislukt en is de taak mogelijk zelfs voltooid. Als u wilt afdwingen dat een runbook stopt bij een niet-afsluit fout, kunt u gebruiken `ErrorAction Stop` op de cmdlet.

## <a name="calling-processes"></a>Processen aanroepen

Runbooks die worden uitgevoerd in azure-sandboxes bieden geen ondersteuning voor het aanroepen van processen, zoals uitvoer bare bestanden (**. exe** ) of subprocessen. De reden hiervoor is dat een Azure sandbox een gedeeld proces is dat wordt uitgevoerd in een container die mogelijk geen toegang heeft tot alle onderliggende Api's. Voor scenario's waarvoor software van derden of aanroepen naar subprocessen is vereist, moet u een runbook uitvoeren op een [Hybrid Runbook worker](automation-hybrid-runbook-worker.md).

## <a name="device-and-application-characteristics"></a>Kenmerken van apparaten en toepassingen

Runbook-taken in azure-sandboxes hebben geen toegang tot de kenmerken van apparaten of toepassingen. De meest voorkomende API die wordt gebruikt om de metrische gegevens over prestaties in Windows op te vragen, is WMI, met een aantal algemene metrische gegevens die geheugen en CPU-gebruik zijn. Het maakt echter niet uit welke API wordt gebruikt, omdat taken die in de cloud worden uitgevoerd, geen toegang hebben tot de micro soft-implementatie van Web-Based Enter prise Management (WBEM). Dit platform is gebaseerd op de Common Information Model (CIM) en biedt de industrie normen voor het definiëren van kenmerken van apparaten en toepassingen.

## <a name="webhooks"></a>Webhooks

Externe services, bijvoorbeeld Azure DevOps Services en GitHub, kunnen een runbook starten in Azure Automation. Voor dit type opstarten gebruikt de service een [webhook](automation-webhooks.md) via één HTTP-aanvraag. Door gebruik te maken van een webhook kunnen runbooks worden gestart zonder implementatie van een volledige Azure Automation-functie.

## <a name="shared-resources"></a><a name="fair-share"></a>Gedeelde resources

Voor het delen van resources tussen alle runbooks in de Cloud maakt Azure gebruik van een concept met de naam fair share. Met een eerlijke share wordt met Azure tijdelijk een taak verwijderd die langer dan drie uur is uitgevoerd. Taken voor [Power shell-runbooks](automation-runbook-types.md#powershell-runbooks) en [python-runbooks](automation-runbook-types.md#python-runbooks) worden gestopt en niet opnieuw gestart en de taak status wordt gestopt.

Voor langlopende Azure Automation taken is het raadzaam om een Hybrid Runbook Worker te gebruiken. Hybrid Runbook Workers worden niet beperkt door een billijke share en hebben geen beperking voor de manier waarop een Runbook kan worden uitgevoerd. De overige taak [limieten](../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits) zijn van toepassing op zowel Azure-sandboxes als Hybrid Runbook Workers. Hoewel Hybrid Runbook Workers niet beperkt zijn tot de drie uur billijke share limiet, moet u runbooks ontwikkelen die worden uitgevoerd op de werk nemers die het opnieuw opstarten van onverwachte problemen met de lokale infra structuur ondersteunen.

Een andere optie is om een runbook te optimaliseren door onderliggende runbooks te gebruiken. Uw runbook kan bijvoorbeeld een lus door lopen op verschillende resources, bijvoorbeeld met een database bewerking in verschillende data bases. U kunt deze functie verplaatsen naar een [onderliggend runbook](automation-child-runbooks.md) en het runbook aanroepen met [Start-AzAutomationRunbook](/powershell/module/az.automation/start-azautomationrunbook). Onderliggende runbooks worden parallel uitgevoerd in afzonderlijke processen.

Als u onderliggende runbooks gebruikt, wordt de totale hoeveelheid tijd voor het volt ooien van het bovenliggende runbook verkleind. Uw runbook kan de cmdlet [Get-AzAutomationJob](/powershell/module/az.automation/get-azautomationjob) gebruiken om de taak status voor een onderliggend runbook te controleren als deze nog meer bewerkingen heeft nadat het onderliggende proces is voltooid.

## <a name="next-steps"></a>Volgende stappen

* Zie [zelf studie: een Power shell-Runbook maken](learn/automation-tutorial-runbook-textual-powershell.md)om aan de slag te gaan met een Power shell-runbook.
* Zie [Runbooks beheren in azure Automation](manage-runbooks.md)voor het werken met runbooks.
* Zie [Power shell docs](/powershell/scripting/overview)(Engelstalig) voor meer informatie over Power shell.
* Zie [Az.Automation](/powershell/module/az.automation#automation) voor een naslagdocumentatie voor een PowerShell-cmdlet.
