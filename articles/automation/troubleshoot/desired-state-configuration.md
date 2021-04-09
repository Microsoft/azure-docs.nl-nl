---
title: Configuratie problemen met Azure Automation status oplossen
description: In dit artikel leest u hoe u problemen met de configuratie van Azure Automationes oplost en oplost.
services: automation
ms.subservice: ''
ms.date: 04/16/2019
ms.topic: troubleshooting
ms.openlocfilehash: e6caf3fed708e89b55a88719ca5358f6174c2ac8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98896525"
---
# <a name="troubleshoot-azure-automation-state-configuration-issues"></a>Configuratie problemen met Azure Automation status oplossen

Dit artikel bevat informatie over het oplossen van problemen die zich voordoen tijdens het compileren of implementeren van configuraties in Azure Automation status configuratie. Zie [Azure Automation status configuratie Overview](../automation-dsc-overview.md)(Engelstalig) voor algemene informatie over de status configuratie functie.

## <a name="diagnose-an-issue"></a>Een probleem vaststellen

Wanneer u een compilatie-of implementatie fout voor de configuratie ontvangt, zijn hier enkele stappen waarmee u het probleem kunt vaststellen.

### <a name="1-ensure-that-your-configuration-compiles-successfully-on-the-local-machine"></a>1. Controleer of de configuratie is gecompileerd op de lokale computer

Azure Automation status configuratie is gebaseerd op Power shell desired state Configuration (DSC). U kunt de documentatie voor de DSC-taal en syntaxis vinden in de [Power shell DSC-documenten](/powershell/scripting/overview).

Door een DSC-configuratie op uw lokale machine te compileren, kunt u veelvoorkomende fouten detecteren en oplossen, zoals:

   - Ontbrekende modules.
   - Syntaxis fouten.
   - Logische fouten.

### <a name="2-view-dsc-logs-on-your-node"></a>2. DSC-logboeken weer geven op uw knoop punt

Als uw configuratie is gecompileerd, maar mislukt als deze wordt toegepast op een knoop punt, kunt u gedetailleerde informatie vinden in de DSC-Logboeken. Zie voor informatie over waar u deze logboeken kunt vinden, [waar de DSC-gebeurtenis logboeken zijn](/powershell/scripting/dsc/troubleshooting/troubleshooting#where-are-dsc-event-logs).

De [xDscDiagnostics](https://github.com/PowerShell/xDscDiagnostics) -module kan u helpen bij het parseren van gedetailleerde informatie uit de DSC-Logboeken. Als u contact opneemt met ondersteuning, moeten deze logboeken uw probleem vaststellen.

U kunt de `xDscDiagnostics` module installeren op uw lokale computer door de instructies te volgen in [de module stabiele versie installeren](https://github.com/PowerShell/xDscDiagnostics#install-the-stable-version-module).

Als u de `xDscDiagnostics` module op uw Azure-machine wilt installeren, gebruikt u [invoke-AzVMRunCommand](/powershell/module/az.compute/invoke-azvmruncommand). U kunt ook de optie **opdracht uitvoeren** in de Azure Portal gebruiken door de stappen in [Power shell-scripts uit te voeren in uw Windows-VM met de opdracht uitvoeren](../../virtual-machines/windows/run-command.md).

Zie voor meer informatie over het gebruik van **XDSCDIAGNOSTICS** [DSC-logboeken analyseren met behulp van xDscDiagnostics](/powershell/scripting/dsc/troubleshooting/troubleshooting#using-xdscdiagnostics-to-analyze-dsc-logs). Zie ook [xDscDiagnostics-cmdlets](https://github.com/PowerShell/xDscDiagnostics#cmdlets).

### <a name="3-ensure-that-nodes-and-the-automation-workspace-have-required-modules"></a>3. Controleer of de knoop punten en de Automation-werk ruimte de vereiste modules hebben

DSC is afhankelijk van de modules die op het knoop punt zijn geïnstalleerd. Wanneer u Azure Automation status configuratie gebruikt, importeert u de benodigde modules in uw Automation-account door de stappen in [import modules](../shared-resources/modules.md#import-modules)te volgen. Configuraties kunnen ook afhankelijk zijn van specifieke versies van modules. Zie [problemen met modules oplossen](shared-resources.md#modules)voor meer informatie.

## <a name="scenario-a-configuration-with-special-characters-cant-be-deleted-from-the-portal"></a><a name="unsupported-characters"></a>Scenario: een configuratie met speciale tekens kan niet worden verwijderd uit de portal

### <a name="issue"></a>Probleem

Wanneer u probeert een DSC-configuratie te verwijderen uit de portal, ziet u de volgende fout:

```error
An error occurred while deleting the DSC configuration '<name>'.  Error-details: The argument configurationName with the value <name> is not valid.  Valid configuration names can contain only letters,  numbers, and underscores.  The name must start with a letter.  The length of the name must be between 1 and 64 characters.
```

### <a name="cause"></a>Oorzaak

Deze fout is een tijdelijk probleem dat is gepland om te worden opgelost.

### <a name="resolution"></a>Oplossing

Gebruik de cmdlet [Remove-AzAutomationDscConfiguration](/powershell/module/Az.Automation/Remove-AzAutomationDscConfiguration) om de configuratie te verwijderen.

## <a name="scenario-failed-to-register-the-dsc-agent"></a><a name="failed-to-register-agent"></a>Scenario: de DSC-agent kan niet worden geregistreerd

### <a name="issue"></a>Probleem

Wanneer u DscLocalConfigurationManager of een andere DSC-cmdlet hebt [ingesteld](/powershell/module/psdesiredstateconfiguration/set-dsclocalconfigurationmanager) , wordt de volgende fout weer gegeven:

```error
Registration of the Dsc Agent with the server
https://<location>-agentservice-prod-1.azure-automation.net/accounts/00000000-0000-0000-0000-000000000000 failed. The
underlying error is: Failed to register Dsc Agent with AgentId 00000000-0000-0000-0000-000000000000 with the server htt
ps://<location>-agentservice-prod-1.azure-automation.net/accounts/00000000-0000-0000-0000-000000000000/Nodes(AgentId='00000000-0000-0000-0000-000000000000'). .
    + CategoryInfo          : InvalidResult: (root/Microsoft/...gurationManager:String) [], CimException
    + FullyQualifiedErrorId : RegisterDscAgentCommandFailed,Microsoft.PowerShell.DesiredStateConfiguration.Commands.Re
   gisterDscAgentCommand
    + PSComputerName        : <computerName>
```

### <a name="cause"></a>Oorzaak

Deze fout wordt meestal veroorzaakt door een firewall, de computer zich achter een proxy server bevindt of andere netwerk fouten.

### <a name="resolution"></a>Oplossing

Controleer of de computer toegang heeft tot de juiste eind punten voor DSC en probeer het opnieuw. Zie [netwerk planning](../automation-dsc-overview.md#network-planning)voor een lijst met poorten en adressen die nodig zijn.

## <a name="a-nameunauthorizedscenario-status-reports-return-the-response-code-unauthorized"></a><a name="unauthorized"><a/>Scenario: status rapporten retour neren ongeldige antwoord code

### <a name="issue"></a>Probleem

Wanneer u een knoop punt registreert met Azure Automation status configuratie, wordt een van de volgende fout berichten weer gegeven:

```error
The attempt to send status report to the server https://{your Automation account URL}/accounts/xxxxxxxxxxxxxxxxxxxxxx/Nodes(AgentId='xxxxxxxxxxxxxxxxxxxxxxxxx')/SendReport returned unexpected response code Unauthorized.
```

```error
VM has reported a failure when processing extension 'Microsoft.Powershell.DSC / Registration of the Dsc Agent with the server failed.
```

### <a name="cause"></a>Oorzaak

Dit probleem wordt veroorzaakt door een onjuist of verlopen certificaat. Zie [een knoop punt opnieuw registreren](../automation-dsc-onboarding.md#re-register-a-node).

Dit probleem kan ook worden veroorzaakt door een proxy configuratie waarmee geen toegang tot ***. Azure-Automation.net** wordt toegestaan. Zie [configuratie van particuliere netwerken](../automation-dsc-overview.md#network-planning)voor meer informatie. 

### <a name="resolution"></a>Oplossing

Voer de volgende stappen uit om het mislukte DSC-knoop punt opnieuw te registreren.

#### <a name="step-1-unregister-the-node"></a>Stap 1: de registratie van het knoop punt ongedaan maken

1. Ga in het Azure Portal naar de **Start**  >  **Automation-accounts** > (uw Automation-account) > **State Configuration (DSC)**.
1. Selecteer **knoop punten** en selecteer het knoop punt dat problemen ondervindt.
1. Selecteer **registratie ongedaan** maken om de registratie van het knoop punt ongedaan te maken.

#### <a name="step-2-uninstall-the-dsc-extension-from-the-node"></a>Stap 2: de DSC-uitbrei ding verwijderen uit het knoop punt

1. Ga in het Azure Portal naar de   >  **virtuele machine** van de thuis computer > (knoop punt mislukt) >- **extensies**.
1. Selecteer **micro soft. Power shell. DSC**, de Power shell DSC-uitbrei ding.
1. Selecteer **verwijderen** om de extensie te verwijderen.

#### <a name="step-3-remove-all-bad-or-expired-certificates-from-the-node"></a>Stap 3: alle beschadigde of verlopen certificaten verwijderen van het knoop punt

Voer de volgende opdrachten uit op het knoop punt waarvoor een verhoogde Power shell-prompt is uitgevoerd:

```powershell
$certs = @()
$certs += dir cert:\localmachine\my | ?{$_.FriendlyName -like "DSC"}
$certs += dir cert:\localmachine\my | ?{$_.FriendlyName -like "DSC-OaaS Client Authentication"}
$certs += dir cert:\localmachine\CA | ?{$_.subject -like "CN=AzureDSCExtension*"}
"";"== DSC Certificates found: " + $certs.Count
$certs | FL ThumbPrint,FriendlyName,Subject
If (($certs.Count) -gt 0)
{ 
    ForEach ($Cert in $certs) 
    {
        RD -LiteralPath ($Cert.Pspath) 
    }
}
```

#### <a name="step-4-reregister-the-failing-node"></a>Stap 4: de registratie van het knoop punt mislukt

1. Ga in het Azure Portal naar de **Start**  >  **Automation-accounts** > (uw Automation-account) > **State Configuration (DSC)**.
1. **Knoop punten** selecteren.
1. Selecteer **Toevoegen**.
1. Selecteer het knoop punt dat niet werkt.
1. Selecteer **verbinding maken** en selecteer de gewenste opties.

## <a name="scenario-node-is-in-failed-status-with-a-not-found-error"></a><a name="failed-not-found"></a>Scenario: het knoop punt heeft de status mislukt met de fout ' niet gevonden '

### <a name="issue"></a>Probleem

Het knoop punt heeft een rapport met de status mislukt en bevat de volgende fout:

```error
The attempt to get the action from server https://<url>//accounts/<account-id>/Nodes(AgentId=<agent-id>)/GetDscAction failed because a valid configuration <guid> cannot be found.
```

### <a name="cause"></a>Oorzaak

Deze fout treedt doorgaans op wanneer het knoop punt is toegewezen aan een configuratie naam, bijvoorbeeld **ABC**, in plaats van de naam van een knooppunt configuratie (MOF-bestand), bijvoorbeeld **ABC. Webserver**.

### <a name="resolution"></a>Oplossing

* Zorg ervoor dat u het knoop punt toewijst met de naam van de knooppunt configuratie en niet de configuratie naam.
* U kunt een knooppunt configuratie toewijzen aan een knoop punt met behulp van de Azure Portal of met een Power shell-cmdlet.

  * Ga in het Azure Portal naar de **Start**  >  **Automation-accounts** > (uw Automation-account) > **State Configuration (DSC)**. Selecteer vervolgens een knoop punt en selecteer **knooppunt configuratie toewijzen**.
  * Gebruik de cmdlet [set-AzAutomationDscNode](/powershell/module/Az.Automation/Set-AzAutomationDscNode) .

## <a name="scenario-no-node-configurations-mof-files-were-produced-when-a-configuration-was-compiled"></a><a name="no-mof-files"></a>Scenario: er zijn geen knooppunt configuraties (MOF-bestanden) geproduceerd tijdens het compileren van een configuratie

### <a name="issue"></a>Probleem

Uw DSC-compilatie taak wordt onderbroken met de volgende fout:

```error
Compilation completed successfully, but no node configuration **.mof** files were generated.
```

### <a name="cause"></a>Oorzaak

Wanneer de expressie die volgt op het `Node` sleutel woord in de DSC `$null` -Configuratie, wordt geëvalueerd naar, worden er geen knooppunt configuraties geproduceerd.

### <a name="resolution"></a>Oplossing

Gebruik een van de volgende oplossingen om het probleem op te lossen:

* Zorg ervoor dat de expressie naast het `Node` sleutel woord in de configuratie definitie niet wordt geëvalueerd naar null.
* Als u [ConfigurationData](../automation-dsc-compile.md) door geven tijdens het compileren van de configuratie, moet u ervoor zorgen dat u de waarden door geven die de configuratie verwacht van de configuratie gegevens.

## <a name="scenario-the-dsc-node-report-becomes-stuck-in-the-in-progress-state"></a><a name="dsc-in-progress"></a>Scenario: het DSC-knooppunt rapport wordt vastgelopen in de status in uitvoering

### <a name="issue"></a>Probleem

De DSC-agent uitvoer:

```error
No instance found with given property values
```

### <a name="cause"></a>Oorzaak

U hebt uw Windows Management Framework-versie (WMF) bijgewerkt en hebt Windows Management Instrumentation (WMI) beschadigd.

### <a name="resolution"></a>Oplossing

Volg de instructies in [DSC bekende problemen en beperkingen](/powershell/scripting/wmf/known-issues/known-issues-dsc).

## <a name="scenario-unable-to-use-a-credential-in-a-dsc-configuration"></a><a name="issue-using-credential"></a>Scenario: een referentie kan niet worden gebruikt in een DSC-configuratie

### <a name="issue"></a>Probleem

De DSC-compilatie taak is onderbroken met de volgende fout:

```error
System.InvalidOperationException error processing property 'Credential' of type <some resource name>: Converting and storing an encrypted password as plaintext is allowed only if PSDscAllowPlainTextPassword is set to true.
```

### <a name="cause"></a>Oorzaak

U hebt een referentie in een configuratie gebruikt, maar u beschikt niet over de juiste `ConfigurationData` `PSDscAllowPlainTextPassword` waarde voor elke knooppunt configuratie.

### <a name="resolution"></a>Oplossing

Zorg ervoor dat de juiste waarde wordt `ConfigurationData` ingesteld op `PSDscAllowPlainTextPassword` True voor elke knooppunt configuratie die wordt vermeld in de configuratie. Zie [DSC-configuraties compileren in de configuratie van de Azure Automation-status](../automation-dsc-compile.md).

## <a name="scenario-failure-processing-extension-error-when-enabling-a-machine-from-a-dsc-extension"></a><a name="failure-processing-extension"></a>Scenario: ' fout bij het verwerken van de uitbrei ding ' bij het inschakelen van een machine vanuit een DSC-uitbrei ding

### <a name="issue"></a>Probleem

Wanneer u een machine inschakelt met behulp van een DSC-extensie, treedt er een fout op die de fout bevat:

```error
VM has reported a failure when processing extension 'Microsoft.Powershell.DSC'. Error message: \"DSC COnfiguration 'RegistrationMetaConfigV2' completed with error(s). Following are the first few: Registration of the Dsc Agent with the server <url> failed. The underlying error is: The attempt to register Dsc Agent with Agent Id <ID> with the server <url> return unexpected response code BadRequest. .\".
```

### <a name="cause"></a>Oorzaak

Deze fout treedt doorgaans op wanneer aan het knoop punt een naam voor de knooppunt configuratie is toegewezen die niet voor komt in de service.

### <a name="resolution"></a>Oplossing

* Zorg ervoor dat u het knoop punt toewijst met een naam die exact overeenkomt met de naam in de service.
* U kunt ervoor kiezen om de naam van de configuratie van het knoop punt niet op te maken, wat resulteert in het inschakelen van het knoop punt, maar geen knooppunt configuratie toewijst.

## <a name="scenario-one-or-more-errors-occurred-error-when-registering-a-node-by-using-powershell"></a><a name="cross-subscription"></a>Scenario: fout ' er zijn een of meer fouten opgetreden ' bij het registreren van een knoop punt met behulp van Power shell

### <a name="issue"></a>Probleem

Wanneer u een knoop punt registreert met behulp [van Regi ster-AzAutomationDSCNode](/powershell/module/az.automation/register-azautomationdscnode) of [REGI ster-AzureRMAutomationDSCNode](/powershell/module/azurerm.automation/register-azurermautomationdscnode), wordt de volgende fout weer gegeven:

```error
One or more errors occurred.
```

### <a name="cause"></a>Oorzaak

Deze fout treedt op wanneer u een knoop punt probeert te registreren in een afzonderlijk abonnement dat wordt gebruikt door het Automation-account.

### <a name="resolution"></a>Oplossing

Het knoop punt voor meerdere abonnementen behandelen alsof het is gedefinieerd voor een afzonderlijke Cloud, of on-premises. Registreer het knoop punt met een van de volgende opties voor het inschakelen van computers:

* Windows: [fysieke/virtuele Windows-machines on-premises of in een andere Cloud dan Azure/AWS](../automation-dsc-onboarding.md#enable-physicalvirtual-windows-machines).
* Linux: [fysieke/virtuele Linux-machines on-premises of in een andere Cloud dan Azure](../automation-dsc-onboarding.md#enable-physicalvirtual-linux-machines).

## <a name="scenario-provisioning-has-failed-error-message"></a><a name="agent-has-a-problem"></a>Scenario: het inrichten is mislukt

### <a name="issue"></a>Probleem

Wanneer u een knoop punt registreert, ziet u de volgende fout:

```error
Provisioning has failed
```

### <a name="cause"></a>Oorzaak

Dit bericht wordt weer gegeven wanneer er een probleem is met de verbinding tussen het knoop punt en Azure.

### <a name="resolution"></a>Oplossing

Bepaal of het knoop punt zich in een virtueel particulier netwerk (VPN) bevindt of dat er andere problemen zijn die verbinding maken met Azure. Zie problemen [met de implementatie van onderdelen oplossen](onboarding.md).

## <a name="scenario-failure-with-a-general-error-when-applying-a-configuration-in-linux"></a><a name="failure-linux-temp-noexec"></a>Scenario: fout met een algemene fout bij het Toep assen van een configuratie in Linux

### <a name="issue"></a>Probleem

Wanneer u een configuratie in Linux toepast, treedt er een fout op die de fout bevat:

```error
This event indicates that failure happens when LCM is processing the configuration. ErrorId is 1. ErrorDetail is The SendConfigurationApply function did not succeed.. ResourceId is [resource]name and SourceInfo is ::nnn::n::resource. ErrorMessage is A general error occurred, not covered by a more specific error code..
```

### <a name="cause"></a>Oorzaak

Als de locatie van de **map/tmp** is ingesteld op `noexec` , kan de huidige versie van DSC geen configuraties Toep assen.

### <a name="resolution"></a>Oplossing

Verwijder de `noexec` optie uit de locatie **map/tmp** .

## <a name="scenario-node-configuration-names-that-overlap-can-result-in-a-bad-release"></a><a name="compilation-node-name-overlap"></a>Scenario: namen van knooppunt configuraties die elkaar overlappen kunnen leiden tot een slechte release

### <a name="issue"></a>Probleem

Wanneer u één configuratie script gebruikt om meerdere knooppunt configuraties te genereren en sommige knooppunt configuratie namen subsets van andere namen zijn, kan de compilatie service uiteindelijk de verkeerde configuratie toewijzen. Dit probleem treedt alleen op wanneer u één script gebruikt voor het genereren van configuraties met configuratie gegevens per knoop punt en alleen wanneer de naam overlapt aan het begin van de teken reeks. Een voor beeld is een configuratie script dat wordt gebruikt voor het genereren van configuraties op basis van knooppunt gegevens die worden door gegeven als hashes met behulp van cmdlets en de knooppunt gegevens bevatten servers met de naam **Server** en **1server**.

### <a name="cause"></a>Oorzaak

Dit is een bekend probleem met de compilatie service.

### <a name="resolution"></a>Oplossing

De beste oplossing is lokaal of in een CI/CD-pijp lijn te compileren en de knooppunt configuratie-MOF-bestanden rechtstreeks te uploaden naar de service. Als de compilatie in de service een vereiste is, is de volgende beste oplossing om de compilatie taken te splitsen zodat er geen overlap ping is in namen.

## <a name="scenario-gateway-timeout-error-on-dsc-configuration-upload"></a><a name="gateway-timeout"></a>Scenario: time-out van gateway bij DSC-configuratie uploaden

#### <a name="issue"></a>Probleem

Er wordt een fout bericht weer gegeven `GatewayTimeout` Wanneer u een DSC-configuratie uploadt. 

### <a name="cause"></a>Oorzaak

DSC-configuraties die lange tijd duren om te compileren, kunnen deze fout veroorzaken.

### <a name="resolution"></a>Oplossing

U kunt ervoor zorgen dat uw DSC-configuraties sneller worden geparseerd door expliciet de `ModuleName` para meter op te nemen voor de aanroepen van de [import-dscresource bieden](/powershell/scripting/dsc/configurations/import-dscresource) .

## <a name="next-steps"></a>Volgende stappen

Als uw probleem hier niet wordt weer gegeven of u het probleem niet kunt oplossen, kunt u een van de volgende kanalen proberen voor aanvullende ondersteuning:

* Krijg antwoorden van Azure-experts via [Azure-forums](https://azure.microsoft.com/support/forums/).
* Maak verbinding met [@AzureSupport](https://twitter.com/azuresupport) , het officiële Microsoft Azure account voor het verbeteren van de gebruikers ervaring. Azure-ondersteuning verbindt de Azure-community met antwoorden, ondersteuning en experts.
* Een ondersteunings incident voor Azure. Ga naar de [ondersteunings site van Azure](https://azure.microsoft.com/support/options/)en selecteer **ondersteuning verkrijgen**.
