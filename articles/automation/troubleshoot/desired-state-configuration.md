---
title: Problemen Azure Automation State Configuration oplossen
description: In dit artikel wordt beschreven hoe u problemen met Azure Automation State Configuration oplossen.
services: automation
ms.subservice: ''
ms.date: 04/16/2019
ms.topic: troubleshooting
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 91c4780981851b62027fecf18da2c3b78625f272
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107831200"
---
# <a name="troubleshoot-azure-automation-state-configuration-issues"></a>Problemen Azure Automation State Configuration oplossen

Dit artikel bevat informatie over het oplossen van problemen die zich voordoen tijdens het compileren of implementeren van configuraties in Azure Automation State Configuration. Zie overzicht van State Configuration voor algemene [informatie over Azure Automation State Configuration functie](../automation-dsc-overview.md).

## <a name="diagnose-an-issue"></a>Een probleem vaststellen

Wanneer u een compilatie- of implementatiefout voor configuratie ontvangt, volgen hier enkele stappen om u te helpen bij het vaststellen van het probleem.

### <a name="1-ensure-that-your-configuration-compiles-successfully-on-the-local-machine"></a>1. Zorg ervoor dat uw configuratie goed wordt ge compileerd op de lokale computer

Azure Automation State Configuration is gebouwd op PowerShell Desired State Configuration (DSC). U vindt de documentatie voor de DSC-taal en syntaxis in [de PowerShell DSC Docs.](/powershell/scripting/overview)

Door een DSC-configuratie op uw lokale computer te compileren, kunt u veelvoorkomende fouten ontdekken en oplossen, zoals:

   - Ontbrekende modules.
   - Syntaxisfouten.
   - Logische fouten.

### <a name="2-view-dsc-logs-on-your-node"></a>2. DSC-logboeken weergeven op uw knooppunt

Als uw configuratie goed wordt gebuiteerd, maar mislukt wanneer deze wordt toegepast op een knooppunt, kunt u gedetailleerde informatie vinden in de DSC-logboeken. Zie Waar zijn de DSC-gebeurtenislogboeken? voor informatie over waar u deze [logboeken kunt vinden.](/powershell/scripting/dsc/troubleshooting/troubleshooting#where-are-dsc-event-logs)

De [xDscDiagnostics-module](https://github.com/PowerShell/xDscDiagnostics) kan u helpen bij het parseren van gedetailleerde informatie uit de DSC-logboeken. Als u contact opvraagt met de ondersteuning, hebben ze deze logboeken nodig om uw probleem vast te stellen.

U kunt de module op uw lokale computer installeren door de instructies te volgen `xDscDiagnostics` in Module met stabiele versie [installeren.](https://github.com/PowerShell/xDscDiagnostics#install-the-stable-version-module)

Gebruik `xDscDiagnostics` [Invoke-AzVMRunCommand](/powershell/module/az.compute/invoke-azvmruncommand)om de module op uw Azure-computer te installeren. U kunt ook de optie **Opdracht** uitvoeren in de Azure Portal door de stappen in [PowerShell-scripts uitvoeren op uw Windows-VM](../../virtual-machines/windows/run-command.md)met Uitvoeropdracht.

Zie Using [xDscDiagnostics to analyze DSC logs (xDscDiagnostics](/powershell/scripting/dsc/troubleshooting/troubleshooting#using-xdscdiagnostics-to-analyze-dsc-logs)gebruiken om DSC-logboeken te analyseren) voor meer informatie over het gebruik van **xDscDiagnostics.** Zie ook [xDscDiagnostics-cmdlets](https://github.com/PowerShell/xDscDiagnostics#cmdlets).

### <a name="3-ensure-that-nodes-and-the-automation-workspace-have-required-modules"></a>3. Zorg ervoor dat knooppunten en de Automation-werkruimte vereiste modules hebben

DSC is afhankelijk van modules die op het knooppunt zijn geïnstalleerd. Wanneer u Azure Automation State Configuration, importeert u alle vereiste modules in uw Automation-account door de stappen in [Modules importeren te volgen.](../shared-resources/modules.md#import-modules) Configuraties kunnen ook afhankelijk zijn van specifieke versies van modules. Zie Troubleshoot modules (Problemen met [modules oplossen) voor meer informatie.](shared-resources.md#modules)

## <a name="scenario-a-configuration-with-special-characters-cant-be-deleted-from-the-portal"></a><a name="unsupported-characters"></a>Scenario: Een configuratie met speciale tekens kan niet worden verwijderd uit de portal

### <a name="issue"></a>Probleem

Wanneer u een DSC-configuratie uit de portal probeert te verwijderen, ziet u de volgende fout:

```error
An error occurred while deleting the DSC configuration '<name>'.  Error-details: The argument configurationName with the value <name> is not valid.  Valid configuration names can contain only letters,  numbers, and underscores.  The name must start with a letter.  The length of the name must be between 1 and 64 characters.
```

### <a name="cause"></a>Oorzaak

Deze fout is een tijdelijk probleem dat is gepland om te worden opgelost.

### <a name="resolution"></a>Oplossing

Gebruik de cmdlet [Remove-AzAutomationDscConfiguration](/powershell/module/Az.Automation/Remove-AzAutomationDscConfiguration) om de configuratie te verwijderen.

## <a name="scenario-failed-to-register-the-dsc-agent"></a><a name="failed-to-register-agent"></a>Scenario: Kan de DSC-agent niet registreren

### <a name="issue"></a>Probleem

Wanneer [Set-DscLocalConfigurationManager](/powershell/module/psdesiredstateconfiguration/set-dsclocalconfigurationmanager) of een andere DSC-cmdlet wordt weergegeven, wordt de volgende fout weergegeven:

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

Deze fout wordt doorgaans veroorzaakt door een firewall, de computer achter een proxyserver of andere netwerkfouten.

### <a name="resolution"></a>Oplossing

Controleer of uw computer toegang heeft tot de juiste eindpunten voor DSC en probeer het opnieuw. Zie Netwerkplanning voor een lijst met benodigde poorten [en adressen.](../automation-dsc-overview.md#network-planning)

## <a name="a-nameunauthorizedscenario-status-reports-return-the-response-code-unauthorized"></a><a name="unauthorized"><a/>Scenario: Statusrapporten retourneren de antwoordcode Niet geautoriseerd

### <a name="issue"></a>Probleem

Wanneer u een knooppunt bij Azure Automation State Configuration registreert, ontvangt u een van de volgende foutberichten:

```error
The attempt to send status report to the server https://{your Automation account URL}/accounts/xxxxxxxxxxxxxxxxxxxxxx/Nodes(AgentId='xxxxxxxxxxxxxxxxxxxxxxxxx')/SendReport returned unexpected response code Unauthorized.
```

```error
VM has reported a failure when processing extension 'Microsoft.Powershell.DSC / Registration of the Dsc Agent with the server failed.
```

### <a name="cause"></a>Oorzaak

Dit probleem wordt veroorzaakt door een beschadigd of verlopen certificaat. Zie [Een knooppunt opnieuw registreren.](../automation-dsc-onboarding.md#re-register-a-node)

Dit probleem kan ook worden veroorzaakt doordat een proxyconfiguratie geen toegang toestaat tot ***.azure-automation.net**. Zie Configuratie van particuliere netwerken [voor meer informatie.](../automation-dsc-overview.md#network-planning) 

### <a name="resolution"></a>Oplossing

Gebruik de volgende stappen om het mislukte DSC-knooppunt opnieuw te registreren.

#### <a name="step-1-unregister-the-node"></a>Stap 1: de registratie van het knooppunt ongedaan maken

1. Ga in Azure Portal naar **Home**  >  **Automation-accounts** > (uw Automation-account) > **State Configuration (DSC)**.
1. Selecteer **Knooppunten** en selecteer het knooppunt met problemen.
1. Selecteer **Registratie ongedaan maken** om de registratie van het knooppunt ongedaan te maken.

#### <a name="step-2-uninstall-the-dsc-extension-from-the-node"></a>Stap 2: de DSC-extensie verwijderen uit het knooppunt

1. Ga in Azure Portal naar **Start**  >  **virtuele machine >** (mislukt knooppunt) > **Extensies.**
1. Selecteer **Microsoft.Powershell.DSC,** de PowerShell DSC-extensie.
1. Selecteer **Verwijderen om** de extensie te verwijderen.

#### <a name="step-3-remove-all-bad-or-expired-certificates-from-the-node"></a>Stap 3: verwijder alle slechte of verlopen certificaten uit het knooppunt

Voer de volgende opdrachten uit op het mislukte knooppunt vanuit een PowerShell-prompt met verhoogde bevoegdheid:

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

#### <a name="step-4-reregister-the-failing-node"></a>Stap 4: het mislukte knooppunt opnieuw registreren

1. Ga in Azure Portal naar **Home**  >  **Automation-accounts** > (uw Automation-account) > **State Configuration (DSC)**.
1. Selecteer **Knooppunten**.
1. Selecteer **Toevoegen**.
1. Selecteer het knooppunt dat mislukt.
1. Selecteer **Verbinding** maken en selecteer de gewenste opties.

## <a name="scenario-node-is-in-failed-status-with-a-not-found-error"></a><a name="failed-not-found"></a>Scenario: Knooppunt heeft de status Mislukt met de fout 'Niet gevonden'

### <a name="issue"></a>Probleem

Het knooppunt heeft een rapport met de status Mislukt en bevat de volgende fout:

```error
The attempt to get the action from server https://<url>//accounts/<account-id>/Nodes(AgentId=<agent-id>)/GetDscAction failed because a valid configuration <guid> cannot be found.
```

### <a name="cause"></a>Oorzaak

Deze fout treedt doorgaans op wanneer het knooppunt wordt toegewezen aan een configuratienaam, bijvoorbeeld **ABC**, in plaats van een naam voor een knooppuntconfiguratie (MOF-bestand), bijvoorbeeld **ABC. WebServer**.

### <a name="resolution"></a>Oplossing

* Zorg ervoor dat u het knooppunt toewijst met de naam van de knooppuntconfiguratie en niet de configuratienaam.
* U kunt een knooppuntconfiguratie toewijzen aan een knooppunt met behulp van de Azure Portal of met een PowerShell-cmdlet.

  * Ga in Azure Portal naar **Home**  >  **Automation-accounts** > (uw Automation-account) > **State Configuration (DSC)**. Selecteer vervolgens een knooppunt en selecteer **Knooppuntconfiguratie toewijzen.**
  * Gebruik de cmdlet [Set-AzAutomationDscNode.](/powershell/module/Az.Automation/Set-AzAutomationDscNode)

## <a name="scenario-no-node-configurations-mof-files-were-produced-when-a-configuration-was-compiled"></a><a name="no-mof-files"></a>Scenario: Er zijn geen knooppuntconfiguraties (MOF-bestanden) geproduceerd toen een configuratie werd gecompileerd

### <a name="issue"></a>Probleem

De DSC-compilatie job wordt opgeschort met de fout:

```error
Compilation completed successfully, but no node configuration **.mof** files were generated.
```

### <a name="cause"></a>Oorzaak

Wanneer de expressie na het `Node` trefwoord in de DSC-configuratie wordt geëvalueerd als `$null` , worden er knooppuntconfiguraties geproduceerd.

### <a name="resolution"></a>Oplossing

Gebruik een van de volgende oplossingen om het probleem op te lossen:

* Zorg ervoor dat de expressie naast het trefwoord in de configuratiedefinitie niet `Node` wordt evalueren op Null.
* Als u [ConfigurationData](../automation-dsc-compile.md) door te geven tijdens het compileren van de configuratie, zorg ervoor dat u de waarden die de configuratie verwacht van de configuratiegegevens door te geven.

## <a name="scenario-the-dsc-node-report-becomes-stuck-in-the-in-progress-state"></a><a name="dsc-in-progress"></a>Scenario: Het DSC-knooppuntrapport loopt vast in de status Wordt uitgevoerd

### <a name="issue"></a>Probleem

De DSC-agent heeft de volgende uitvoer:

```error
No instance found with given property values
```

### <a name="cause"></a>Oorzaak

U hebt uw WMF-Windows Management Framework (WMF) bijgewerkt en hebt de Windows Management Instrumentation (WMI) beschadigd.

### <a name="resolution"></a>Oplossing

Volg de instructies in [Bekende problemen en beperkingen van DSC.](/powershell/scripting/wmf/known-issues/known-issues-dsc)

## <a name="scenario-unable-to-use-a-credential-in-a-dsc-configuration"></a><a name="issue-using-credential"></a>Scenario: Kan geen referentie gebruiken in een DSC-configuratie

### <a name="issue"></a>Probleem

De DSC-compilatie job is tijdelijk opgeschort met de volgende fout:

```error
System.InvalidOperationException error processing property 'Credential' of type <some resource name>: Converting and storing an encrypted password as plaintext is allowed only if PSDscAllowPlainTextPassword is set to true.
```

### <a name="cause"></a>Oorzaak

U hebt een referentie in een configuratie gebruikt, maar u hebt niet de juiste informatie verstrekt om voor elke knooppuntconfiguratie in te stellen `ConfigurationData` `PSDscAllowPlainTextPassword` op true.

### <a name="resolution"></a>Oplossing

Zorg ervoor dat u de juiste door te geven om in te stellen op true voor elke `ConfigurationData` `PSDscAllowPlainTextPassword` knooppuntconfiguratie die wordt vermeld in de configuratie. Zie [Compiling DSC configurations in Azure Automation State Configuration](../automation-dsc-compile.md).

## <a name="scenario-failure-processing-extension-error-when-enabling-a-machine-from-a-dsc-extension"></a><a name="failure-processing-extension"></a>Scenario: Fout bij het verwerken van extensies bij het inschakelen van een machine vanuit een DSC-extensie

### <a name="issue"></a>Probleem

Wanneer u een machine inschakelen met behulp van een DSC-extensie, treedt er een fout op die de volgende fout bevat:

```error
VM has reported a failure when processing extension 'Microsoft.Powershell.DSC'. Error message: \"DSC COnfiguration 'RegistrationMetaConfigV2' completed with error(s). Following are the first few: Registration of the Dsc Agent with the server <url> failed. The underlying error is: The attempt to register Dsc Agent with Agent Id <ID> with the server <url> return unexpected response code BadRequest. .\".
```

### <a name="cause"></a>Oorzaak

Deze fout treedt meestal op wanneer aan het knooppunt een knooppuntconfiguratienaam wordt toegewezen die niet in de service bestaat.

### <a name="resolution"></a>Oplossing

* Zorg ervoor dat u het knooppunt toewijst met een naam die exact overeenkomt met de naam in de service.
* U kunt ervoor kiezen om de naam van de knooppuntconfiguratie niet op te nemen, wat resulteert in het inschakelen van het knooppunt, maar niet het toewijzen van een knooppuntconfiguratie.

## <a name="scenario-one-or-more-errors-occurred-error-when-registering-a-node-by-using-powershell"></a><a name="cross-subscription"></a>Scenario: Fout 'Er zijn een of meer fouten opgetreden' bij het registreren van een knooppunt met behulp van PowerShell

### <a name="issue"></a>Probleem

Wanneer u een knooppunt registreert met behulp van [Register-AzAutomationDSCNode](/powershell/module/az.automation/register-azautomationdscnode) of [Register-AzureRMAutomationDSCNode,](/powershell/module/azurerm.automation/register-azurermautomationdscnode)wordt de volgende fout weergegeven:

```error
One or more errors occurred.
```

### <a name="cause"></a>Oorzaak

Deze fout treedt op wanneer u een knooppunt probeert te registreren in een afzonderlijk abonnement dat wordt gebruikt door het Automation-account.

### <a name="resolution"></a>Oplossing

Behandel het knooppunt voor verschillende abonnementen alsof het is gedefinieerd voor een afzonderlijke cloud of on-premises. Registreer het knooppunt met behulp van een van deze opties voor het inschakelen van computers:

* Windows: [fysieke/virtuele Windows-machines on-premises of in een andere cloud dan Azure/AWS.](../automation-dsc-onboarding.md#enable-physicalvirtual-windows-machines)
* Linux: [fysieke/virtuele Linux-machines on-premises of in een andere cloud dan Azure](../automation-dsc-onboarding.md#enable-physicalvirtual-linux-machines).

## <a name="scenario-provisioning-has-failed-error-message"></a><a name="agent-has-a-problem"></a>Scenario: Foutbericht dat de inrichting is mislukt

### <a name="issue"></a>Probleem

Wanneer u een knooppunt registreert, ziet u de volgende fout:

```error
Provisioning has failed
```

### <a name="cause"></a>Oorzaak

Dit bericht treedt op wanneer er een probleem is met de connectiviteit tussen het knooppunt en Azure.

### <a name="resolution"></a>Oplossing

Bepaal of uw knooppunt zich in een virtueel particulier netwerk (VPN) of andere problemen voor het maken van verbinding met Azure heeft. Zie [Problemen met functie-implementatie oplossen.](onboarding.md)

## <a name="scenario-failure-with-a-general-error-when-applying-a-configuration-in-linux"></a><a name="failure-linux-temp-noexec"></a>Scenario: Fout met een algemene fout bij het toepassen van een configuratie in Linux

### <a name="issue"></a>Probleem

Wanneer u een configuratie in Linux gebruikt, treedt er een fout op die de volgende fout bevat:

```error
This event indicates that failure happens when LCM is processing the configuration. ErrorId is 1. ErrorDetail is The SendConfigurationApply function did not succeed.. ResourceId is [resource]name and SourceInfo is ::nnn::n::resource. ErrorMessage is A general error occurred, not covered by a more specific error code..
```

### <a name="cause"></a>Oorzaak

Als de **/tmp-locatie** is ingesteld op , kan de huidige versie van `noexec` DSC geen configuraties toepassen.

### <a name="resolution"></a>Oplossing

Verwijder de `noexec` optie uit de **/tmp-locatie.**

## <a name="scenario-node-configuration-names-that-overlap-can-result-in-a-bad-release"></a><a name="compilation-node-name-overlap"></a>Scenario: Namen van knooppuntconfiguraties die overlappen, kunnen leiden tot een slechte release

### <a name="issue"></a>Probleem

Wanneer u één configuratiescript gebruikt om meerdere knooppuntconfiguraties te genereren en sommige knooppuntconfiguratienamen subsets van andere namen zijn, kan de compilatieservice uiteindelijk de verkeerde configuratie toewijzen. Dit probleem treedt alleen op wanneer u één script gebruikt om configuraties met configuratiegegevens per knooppunt te genereren en alleen wanneer de naam overlapt aan het begin van de tekenreeks. Een voorbeeld is een enkel configuratiescript dat wordt gebruikt voor het genereren van configuraties op basis van knooppuntgegevens die worden doorgegeven als een hashtabel met behulp van cmdlets, en de knooppuntgegevens bevatten servers met de naam **server** en **1server.**

### <a name="cause"></a>Oorzaak

Dit is een bekend probleem met de compilatieservice.

### <a name="resolution"></a>Oplossing

De beste oplossing is om lokaal of in een CI/CD-pijplijn te compileren en de MOF-bestanden voor knooppuntconfiguratie rechtstreeks naar de service te uploaden. Als compilatie in de service een vereiste is, is de volgende beste oplossing het splitsen van de compilatietaken, zodat namen elkaar niet overlappen.

## <a name="scenario-gateway-timeout-error-on-dsc-configuration-upload"></a><a name="gateway-timeout"></a>Scenario: Time-outfout voor gateway bij uploaden van DSC-configuratie

#### <a name="issue"></a>Probleem

Er wordt een `GatewayTimeout` foutbericht weergegeven wanneer u een DSC-configuratie uploadt. 

### <a name="cause"></a>Oorzaak

DSC-configuraties die lang duren om te compileren, kunnen deze fout veroorzaken.

### <a name="resolution"></a>Oplossing

U kunt uw DSC-configuraties sneller parseren door expliciet de parameter op te nemen `ModuleName` voor [import-DSCResource-aanroepen.](/powershell/scripting/dsc/configurations/import-dscresource)

## <a name="next-steps"></a>Volgende stappen

Als u het probleem hier niet ziet of als u het probleem niet kunt oplossen, probeert u een van de volgende kanalen voor aanvullende ondersteuning:

* Krijg antwoorden van Azure-experts via [Azure-forums.](https://azure.microsoft.com/support/forums/)
* Maak verbinding met [@AzureSupport](https://twitter.com/azuresupport) , het officiële Microsoft Azure account voor het verbeteren van de klantervaring. Ondersteuning voor Azure maakt de Azure-community verbinding met antwoorden, ondersteuning en experts.
* Een incident ondersteuning voor Azure indienen. Ga naar de [ondersteuning voor Azure site](https://azure.microsoft.com/support/options/)en selecteer **Ondersteuning krijgen.**
